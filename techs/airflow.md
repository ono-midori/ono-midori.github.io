# The Application of Airflow

Airflow is a management and execution tool for task pipeline. It has convenient features such as remote execution, task communication, manual control, and task monitoring. Airflow is based on Python, so it's simple to install and deploy.

## Terminologies

- DAG: directed acyclic graph, which is a concept borrowed from graph algorithm, describes a pipeline and the relationships between tasks. DAGs are defined in Python source files and in global space. The DAG files should be located under `~/airflow/dags`, which can be automatically detected and executed by Airflow database and scheduler. DAG file is configuration file written in Python.
- Task: the unit of execution. A task is a node in DAG. Task is implemented by Airflow operators. `PythonOperator` implements python callable object as task. `BashOperators` implements bash command or bash script as task. `SSHOperator` implements remote execution job.
- Airflow scheduler: a daemon process that execute the task at the defined time and in the defined order.
- Airflow web server: a web service that can visualize the execution status and history of all DAGs.
- Back-end database: Airflow needs a back-end relational database for task status management. The default database is sqlite, but since sqlite does not support multiple concurrent connection, it can only work with `SequentialExecutor`. In this way, independent tasks in DAG cannot be executed in parallel. In production deployment, `LocalExecutor` with MySQL or Postgresql is recommended. 
- Celery executor: an Airflow executor that can control a number of workers deployed in multiple host servers. Celery executor is deployed at each server, and tasks can be executed by the worker on each server. A worker can also be defined for receive interested DAGs only by specifying the `queue`. 
- Execution date: the logical date that is meaningful for specific task. Execution date can be extracted using the `ds` Jinja template and for business procedure. If manually specified, the execution date should be after the `start_date` of DAGs. The actual running time of DAGs is determined by the `start_date` and `schedule_interval`, if tasks are launched by Airflow scheduler.

## Important Concepts

- Task should be atomic and independent. A task cannot be splitted into more task, and generally should not has communication with other task. But airflow provides `XCom` mechanism for task communication. It pickles the output of upstream task and push it into an object queue. Downstream task can pull and deserialize the object.
- Airflow DAG can only be scheduled and executed if is not paused, and an Airflow scheduler is running.

## Caveats

- Airflow 2.0.0 has distinguished features from 1.x versions.

## Task Execution Process

- Airflow scheduler should be run as daemon process. The log file should be specified to a proper location, which contains the launch information for each well-defined DAG. It is recommended to redirect stdout and sterr to the log file as well. DAGs are automatically detected and imported by scheduler without manually run the DAG python file. If DAG is not well-defined, the error message or exception is dumped into the log file. For DAGs that are scheduled to run, the execution information is written into `~/airflow/logs/some_dag_name/some_task_name/execution_time/number.log` file. 

## Examples

### Simple Bash Task



### Simple Python Task



### Sending Email and XCom



### Remote Task on SSH



## Recommended Configurations

```ini
[core]
default_timezone = system
load_example = False
executor = LocalExecutor
sql_alchemy_conn = postgresql+psycopg2://airflow:airflow@localhost/airflow

[scheduler]
scheduler_heartbeat_sec = 1

[smtp]
smtp_starttls = False
smtp_ssl = True

```

## Installation Guides

### Postgresql

- The version of Postgresql should be higher than 10 for running Airflow scheduler.

- Apt install `postgresql` and `postgresql-contrib` to use install Postgresql service.

- Add `127.0.0.1 postgresql` line to `/etc/hosts`. If `CeleryExecutor` with Redis is used, add `127.0.0.1 redis` line to the file.

- Modify the `md5` to `trust` in IPv4 local connections section of `/etc/postgresql/10/main/pg_hba.conf`.

- For creation database and tables:

  ```
  postgres=# CREATE USER airflow PASSWORD 'airflow';
  CREATE ROLE
  postgres=# CREATE DATABASE airflow;
  CREATE DATABASE
  postgres=# GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO airflow;
  GRANT
  ```

### SSHOperator

- Pip install `airflow[ssh]` and `paramiko` to use `SSHOperator`.
- Use `airflow connections` command to add and delete SSH connections.



