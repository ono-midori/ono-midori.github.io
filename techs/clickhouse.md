# The Application and Performance of Clickhouse
- [The Application and Performance of Clickhouse](#the-application-and-performance-of-clickhouse)
  - [Introduction](#introduction)
    - [Overview](#overview)
      - [What Is ClickHouse?](#what-is-clickhouse)
      - [Key Properties of OLAP Scenario](#key-properties-of-olap-scenario)
      - [Why Column-Oriented Databases Work Better in the OLAP Scenario](#why-column-oriented-databases-work-better-in-the-olap-scenario)
        - [Input/output](#inputoutput)
        - [CPU](#cpu)
    - [Distinctive Features of ClickHouse](#distinctive-features-of-clickhouse)
      - [True Column-Oriented Database Management System](#true-column-oriented-database-management-system)
      - [Data Compression](#data-compression)
      - [Disk Storage of Data](#disk-storage-of-data)
      - [Parallel Processing on Multiple Cores](#parallel-processing-on-multiple-cores)
      - [Distributed Processing on Multiple Servers](#distributed-processing-on-multiple-servers)
      - [SQL Support](#sql-support)
      - [Vector Computation Engine](#vector-computation-engine)
      - [Real-time Data Updates](#real-time-data-updates)
      - [Primary Index](#primary-index)
      - [Secondary Indexes](#secondary-indexes)
      - [Suitable for Online Queries](#suitable-for-online-queries)
      - [Support for Approximated Calculations](#support-for-approximated-calculations)
      - [Adaptive Join Algorithm](#adaptive-join-algorithm)
      - [Data Replication and Data Integrity Support](#data-replication-and-data-integrity-support)
      - [Role-Based Access Control](#role-based-access-control)
      - [Features that Can Be Considered Disadvantages](#features-that-can-be-considered-disadvantages)
    - [Performance](#performance)
      - [Throughput for a Single Large Query](#throughput-for-a-single-large-query)
      - [Latency When Processing Short Queries](#latency-when-processing-short-queries)
      - [Throughput When Processing a Large Quantity of Short Queries](#throughput-when-processing-a-large-quantity-of-short-queries)
      - [Performance When Inserting Data](#performance-when-inserting-data)
  - [Getting Started](#getting-started)
    - [Example Datasets](#example-datasets)
    - [Installation](#installation)
      - [System Requirements](#system-requirements)
      - [Available Installation Options](#available-installation-options)
        - [From DEB Packages](#from-deb-packages)
        - [Launch](#launch)
    - [Tutorial](#tutorial)
      - [What to Expect from This Tutorial?](#what-to-expect-from-this-tutorial)
      - [Single Node Setup](#single-node-setup)
      - [Import Sample Dataset](#import-sample-dataset)
        - [Download and Extract Table Data](#download-and-extract-table-data)
        - [Create Tables](#create-tables)
        - [Import Data](#import-data)
  - [Interfaces](#interfaces)
    - [Command-line Client](#command-line-client)
      - [Usage](#usage)
        - [Queries with Parameters](#queries-with-parameters)
        - [Query Syntax](#query-syntax)
        - [Example](#example)
        - [Configuring](#configuring)
        - [Configuration Files](#configuration-files)
    - [Native Interface (TCP)](#native-interface-tcp)
    - [HTTP Interface](#http-interface)
  - [Engines](#engines)
    - [Table Engines](#table-engines)
      - [Engine Families](#engine-families)
        - [MergeTree](#mergetree)
        - [Log](#log)
        - [Integration Engines](#integration-engines)
        - [Special Engines](#special-engines)
        - [Virtual Columns](#virtual-columns)
    - [MergeTree Engine Family](#mergetree-engine-family)
    - [MergeTree](#mergetree-1)
      - [Creating a Table](#creating-a-table)
        - [Query Clauses](#query-clauses)
      - [Example of Sections Setting](#example-of-sections-setting)
      - [Data Storage](#data-storage)
      - [Primary Keys and Indexes in Queries](#primary-keys-and-indexes-in-queries)
      - [Selecting the Primary Key](#selecting-the-primary-key)
      - [Choosing a Primary Key that Differs from the Sorting Key](#choosing-a-primary-key-that-differs-from-the-sorting-key)
      - [Use of Indexes and Partitions in Queries](#use-of-indexes-and-partitions-in-queries)
      - [Use of Index for Partially-monotonic Primary Keys](#use-of-index-for-partially-monotonic-primary-keys)
      - [Data Skipping Indexes](#data-skipping-indexes)
        - [Available Types of Indices](#available-types-of-indices)
        - [Functions Support](#functions-support)
      - [Concurrent Data Access](#concurrent-data-access)
      - [TTL for Columns and Tables](#ttl-for-columns-and-tables)
        - [Column TTL](#column-ttl)
        - [Table TTL](#table-ttl)
        - [Removing Data](#removing-data)
      - [Using Multiple Block Devices for Data Storage](#using-multiple-block-devices-for-data-storage)
        - [Introduction](#introduction-1)
        - [Terms](#terms)
        - [Configuration](#configuration)
        - [Details](#details)
        - [Using S3 for Data Storage](#using-s3-for-data-storage)
    - [Data Replication](#data-replication)
      - [Creating Replicated Tables](#creating-replicated-tables)
      - [Recovery After Failures](#recovery-after-failures)
      - [Recovery After Complete Data Loss](#recovery-after-complete-data-loss)
      - [Converting from MergeTree to ReplicatedMergeTree](#converting-from-mergetree-to-replicatedmergetree)
      - [Converting from ReplicatedMergeTree to MergeTree](#converting-from-replicatedmergetree-to-mergetree)
      - [Recovery When Metadata in the Zookeeper Cluster Is Lost or Damaged](#recovery-when-metadata-in-the-zookeeper-cluster-is-lost-or-damaged)
    - [Custom Partitioning Key](#custom-partitioning-key)
    - [ReplacingMergeTree](#replacingmergetree)
      - [Creating a Table](#creating-a-table-1)
    - [SummingMergeTree](#summingmergetree)
      - [Creating a Table](#creating-a-table-2)
      - [Usage Example](#usage-example)
      - [Data Processing](#data-processing)
        - [Common Rules for Summation](#common-rules-for-summation)
        - [The Summation in the Aggregatefunction Columns](#the-summation-in-the-aggregatefunction-columns)
        - [Nested Structures](#nested-structures)
    - [Aggregatingmergetree](#aggregatingmergetree)
      - [Creating a Table](#creating-a-table-3)
      - [SELECT and INSERT](#select-and-insert)
      - [Example of an Aggregated Materialized View](#example-of-an-aggregated-materialized-view)
    - [CollapsingMergeTree](#collapsingmergetree)
      - [Creating a Table](#creating-a-table-4)
      - [Collapsing](#collapsing)
        - [Data](#data)
        - [Algorithm](#algorithm)
      - [Example of Use](#example-of-use)
      - [Example of Another Approach](#example-of-another-approach)
    - [VersionedCollapsingMergeTree](#versionedcollapsingmergetree)
      - [Creating a Table](#creating-a-table-5)
      - [Collapsing](#collapsing-1)
        - [Data](#data-1)
        - [Algorithm](#algorithm-1)
      - [Selecting Data](#selecting-data)
        - [Example of Use](#example-of-use-1)
    - [GraphiteMergeTree](#graphitemergetree)
      - [Creating a Table](#creating-a-table-6)
      - [Rollup Configuration](#rollup-configuration)
        - [Required Columns](#required-columns)
        - [Patterns](#patterns)
        - [Configuration Example](#configuration-example)
    - [Log Engine Family](#log-engine-family)
      - [Common Properties](#common-properties)
      - [Differences](#differences)
    - [Stripelog](#stripelog)
      - [Creating a Table](#creating-a-table-7)
      - [Writing the Data](#writing-the-data)
      - [Reading the Data](#reading-the-data)
      - [Example of Use](#example-of-use-2)
    - [Log](#log-1)
    - [TinyLog](#tinylog)
    - [Distributed Table Engine](#distributed-table-engine)
      - [Note](#note)
        - [Durability settings (fsync_...):](#durability-settings-fsync_)
      - [Virtual Columns](#virtual-columns-1)
    - [Dictionary Table Engine](#dictionary-table-engine)
    - [Merge Table Engine](#merge-table-engine)
      - [Virtual Columns](#virtual-columns-2)
    - [File Table Engine](#file-table-engine)
      - [Usage in ClickHouse Server](#usage-in-clickhouse-server)
      - [Usage in ClickHouse-local](#usage-in-clickhouse-local)
      - [Details of Implementation](#details-of-implementation)
    - [Null Table Engine](#null-table-engine)
    - [Set Table Engine](#set-table-engine)
      - [Limitations and Settings](#limitations-and-settings)
    - [Join Table Engine](#join-table-engine)
      - [Creating a Table](#creating-a-table-8)
      - [Table Usage](#table-usage)
      - [Selecting and Inserting Data](#selecting-and-inserting-data)
      - [Limitations and Settings](#limitations-and-settings-1)
      - [Data Storage](#data-storage-1)
    - [URL Table Engine](#url-table-engine)
      - [Usage](#usage-1)
      - [Example](#example-1)
      - [Details of Implementation](#details-of-implementation-1)
    - [View Table Engine](#view-table-engine)
    - [MaterializedView Table Engine](#materializedview-table-engine)
    - [Memory Table Engine](#memory-table-engine)
    - [Buffer Table Engine](#buffer-table-engine)
    - [External Data for Query Processing](#external-data-for-query-processing)
    - [GenerateRandom Table Engine](#generaterandom-table-engine)
      - [Usage in ClickHouse Server](#usage-in-clickhouse-server-1)
      - [Example](#example-2)

## Introduction

### Overview

#### What Is ClickHouse?

ClickHouse® is a column-oriented database management system (DBMS) for online analytical processing of queries (OLAP).

In a "normal" row-oriented DBMS, data is stored in this order:

![](./pics/clickhouse/row_DBMS.png)

In other words, all the values related to a row are physically stored next to each other.

Examples of a row-oriented DBMS are MySQL, Postgres, and MS SQL Server.

In a column-oriented DBMS, data is stored like this:

![](./pics/clickhouse/column_DBMS.png)

These examples only show the order that data is arranged in. The values from different columns are stored separately, and data from the same column is stored together.

Examples of a column-oriented DBMS: Vertica, Paraccel (Actian Matrix and Amazon Redshift), Sybase IQ, Exasol, Infobright, InfiniDB, MonetDB (VectorWise and Actian Vector), LucidDB, SAP HANA, Google Dremel, Google PowerDrill, Druid, and kdb+.

Different orders for storing data are better suited to different scenarios. The data access scenario refers to what queries are made, how often, and in what proportion; how much data is read for each type of query – rows, columns, and bytes; the relationship between reading and updating data; the working size of the data and how locally it is used; whether transactions are used, and how isolated they are; requirements for data replication and logical integrity; requirements for latency and throughput for each type of query, and so on.

*The higher the load on the system, the more important it is to customize the system set up to match the requirements of the usage scenario, and the more fine grained this customization becomes. There is no system that is equally well-suited to significantly different scenarios. If a system is adaptable to a wide set of scenarios, under a high load, the system will handle all the scenarios equally poorly, or will work well for just one or few of possible scenarios.*

#### Key Properties of OLAP Scenario

- The vast majority of requests are for read access.
- Data is updated in fairly large batches (> 1000 rows), not by single rows; or it is not updated at all.
- Data is added to the DB but is not modified.
- For reads, quite a large number of rows are extracted from the DB, but only a small subset of columns.
- Tables are "wide," meaning they contain a large number of columns.
- Queries are relatively rare (usually hundreds of queries per server or less per second).
- For simple queries, latencies around 50 ms are allowed.
- Column values are fairly small: numbers and short strings (for example, 60 bytes per URL).
- Requires high throughput when processing a single query (up to billions of rows per second per server).
- Transactions are not necessary.
- Low requirements for data consistency.
- There is one large table per query. All tables are small, except for one.
- A query result is significantly smaller than the source data. In other words, data is filtered or aggregated, so the result fits in a single server's RAM.

It is easy to see that the OLAP scenario is very different from other popular scenarios (such as OLTP or Key-Value access). So it doesn't make sense to try to use OLTP or a Key-Value DB for processing analytical queries if you want to get decent performance. For example, if you try to use MongoDB or Redis for analytics, you will get very poor performance compared to OLAP databases.

#### Why Column-Oriented Databases Work Better in the OLAP Scenario

Column-oriented databases are better suited to OLAP scenarios: they are at least 100 times faster in processing most queries. 

##### Input/output

- For an analytical query, only a small number of table columns need to be read. In a column-oriented database, you can read just the data you need. For example, if you need 5 columns out of 100, you can expect a 20-fold reduction in I/O.
- Since data is read in packets, it is easier to compress. Data in columns is also easier to compress. This further reduces the I/O volume.
- Due to the reduced I/O, more data fits in the system cache.

For example, the query "count the number of records for each advertising platform" requires reading one "advertising platform ID" column, which takes up 1 byte uncompressed. If most of the traffic was not from advertising platforms, you can expect at least 10-fold compression of this column. When using a quick compression algorithm, data decompression is possible at a speed of at least several gigabytes of uncompressed data per second. In other words, this query can be processed at a speed of approximately several billion rows per second on a single server. This speed is actually achieved in practice.

##### CPU

Since executing a query requires processing a large number of rows, it helps to dispatch all operations for entire vectors instead of for separate rows, or to implement the query engine so that there is almost no dispatching cost. If you don't do this, with any half-decent disk subsystem, the query interpreter inevitably stalls the CPU. It makes sense to both store data in columns and process it, when possible, by columns.

There are two ways to do this:

- A vector engine. All operations are written for vectors, instead of for separate values. This means you don't need to call operations very often, and dispatching costs are negligible. Operation code contains an optimized internal cycle.
- Code generation. The code generated for the query has all the indirect calls in it.

This is not done in "normal" databases, because it doesn't make sense when running simple queries. However, there are exceptions. For example, MemSQL uses code generation to reduce latency when processing SQL queries. (For comparison, analytical DBMSs require optimization of throughput, not latency.)

Note that for CPU efficiency, the query language must be declarative (SQL or MDX), or at least a vector (J, K). The query should only contain implicit loops, allowing for optimization.

> I didn't understand the whole CPU section.

### Distinctive Features of ClickHouse

#### True Column-Oriented Database Management System

In a real column-oriented DBMS, no extra data is stored with the values. Among other things, this means that constant-length values must be supported, to avoid storing their length "number" next to the values. For example, a billion UInt8-type values should consume around 1 GB uncompressed, or this strongly affects the CPU use. It is essential to store data compactly (without any "garbage") even when uncompressed since the speed of decompression (CPU usage) depends mainly on the volume of uncompressed data.

It is worth noting because there are systems that can store values of different columns separately, *but that can't effectively process analytical queries due to their optimization for other scenarios*. Examples are HBase, BigTable, Cassandra, and HyperTable. You would get throughput around a hundred thousand rows per second in these systems, but not hundreds of millions of rows per second.

> But analytical queries are not necessary for financial data?

It's also worth noting that ClickHouse is a database management system, not a single database. ClickHouse allows creating tables and databases in runtime, loading data, and running queries without reconfiguring and restarting the server.

#### Data Compression 

Some column-oriented DBMSs do not use data compression. However, data compression does play a key role in achieving excellent performance.

In addition to efficient general-purpose compression codecs with different trade-offs between disk space and CPU consumption, ClickHouse provides *specialized codecs* for specific kinds of data, which allow ClickHouse to compete with and outperform more niche databases, like time-series ones.

#### Disk Storage of Data

Keeping data physically sorted by primary key makes it possible to extract data for its specific values or value ranges with low latency, less than a few dozen milliseconds. Some column-oriented DBMSs (such as SAP HANA and Google PowerDrill) can only work in RAM. This approach encourages the allocation of a larger hardware budget than is necessary for real-time analysis.

ClickHouse is designed to work on regular hard drives, which means the cost per GB of data storage is low, but SSD and additional RAM are also fully used if available.

#### Parallel Processing on Multiple Cores

Large queries are parallelized naturally, taking all the necessary resources available on the current server.

> What does this mean?

#### Distributed Processing on Multiple Servers

Almost none of the columnar DBMSs mentioned above have support for distributed query processing.

In ClickHouse, data can reside on different shards. Each shard can be a group of replicas used for fault tolerance. All shards are used to run a query in parallel, transparently for the user.

#### SQL Support 

ClickHouse supports a declarative query language based on SQL that is identical to the ANSI SQL standard in many cases.

Supported queries include `GROUP BY`, `ORDER BY`, subqueries in `FROM`, `JOIN` clause, `IN` operator, and scalar subqueries.

Correlated (dependent) subqueries and window functions are not supported at the time of writing but might become available in the future.

#### Vector Computation Engine 

Data is not only stored by columns but is processed by vectors (parts of columns), which allows achieving high CPU efficiency.

#### Real-time Data Updates 

ClickHouse supports tables with a primary key. To quickly perform queries on the range of the primary key, the data is sorted incrementally using the merge tree. Due to this, data can continually be added to the table. No locks are taken when new data is ingested.

#### Primary Index 

Having a data physically sorted by primary key makes it possible to extract data for its specific values or value ranges with low latency, less than a few dozen milliseconds.

#### Secondary Indexes 

Unlike other database management systems, secondary indexes in ClickHouse does not point to specific rows or row ranges. Instead, they allow the database to know in advance that all rows in some data parts wouldn't match the query filtering conditions and do not read them at all, thus they are called data skipping indexes.

#### Suitable for Online Queries 

Most OLAP database management systems don't aim for online queries with sub-second latencies. In alternative systems, report building time of tens of seconds or even minutes is often considered acceptable. Sometimes it takes even more which forces to prepare reports offline (in advance or by responding with "come back later").

In ClickHouse low latency means that queries can be processed without delay and without trying to prepare an answer in advance, right at the same moment while the user interface page is loading. In other words, online.

#### Support for Approximated Calculations 

ClickHouse provides various ways to trade accuracy for performance:

- Aggregate functions for approximated calculation of the number of distinct values, medians, and quantiles.
- Running a query based on a part (sample) of data and getting an approximated result. In this case, proportionally less data is retrieved from the disk.
- Running an aggregation for a limited number of random keys, instead of for all keys. Under certain conditions for key distribution in the data, this provides a reasonably accurate result while using fewer resources.

#### Adaptive Join Algorithm 

ClickHouse adaptively chooses how to `JOIN` multiple tables, by preferring hash-join algorithm and falling back to the merge-join algorithm if there's more than one large table.

#### Data Replication and Data Integrity Support 

ClickHouse uses asynchronous multi-master replication. After being written to any available replica, all the remaining replicas retrieve their copy in the background. The system maintains identical data on different replicas. Recovery after most failures is performed automatically, or semi-automatically in complex cases.

For more information, see the section Data replication.

#### Role-Based Access Control 

ClickHouse implements user account management using SQL queries and allows for role-based access control configuration similar to what can be found in ANSI SQL standard and popular relational database management systems.

#### Features that Can Be Considered Disadvantages 

- No full-fledged transactions.
- Lack of ability to modify or delete already inserted data with a high rate and low latency. There are batch deletes and updates available to clean up or modify data, for example, to comply with GDPR.
- The sparse index makes ClickHouse not so efficient for point queries retrieving single rows by their keys.

### Performance

According to internal testing results at Yandex, ClickHouse shows the best performance (both the highest throughput for long queries and the lowest latency on short queries) for comparable operating scenarios among systems of its class that were available for testing. You can view the test results on a separate page.

Numerous independent benchmarks came to similar conclusions. They are not difficult to find using an internet search, or you can see our small collection of related links.

#### Throughput for a Single Large Query

Throughput can be measured in rows per second or megabytes per second. If the data is placed in the page cache, a query that is not too complex is processed on modern hardware at a speed of approximately 2-10 GB/s of uncompressed data on a single server (for the most straightforward cases, the speed may reach 30 GB/s). If data is not placed in the page cache, the speed depends on the disk subsystem and the data compression rate. For example, if the disk subsystem allows reading data at 400 MB/s, and the data compression rate is 3, the speed is expected to be around 1.2 GB/s. To get the speed in rows per second, divide the speed in bytes per second by the total size of the columns used in the query. For example, if 10 bytes of columns are extracted, the speed is expected to be around 100-200 million rows per second.

> Network latency is not counted in.

The processing speed increases almost linearly for distributed processing, but only if the number of rows resulting from aggregation or sorting is not too large.

#### Latency When Processing Short Queries 

If a query uses a primary key and does not select too many columns and rows to process (hundreds of thousands), you can expect less than 50 milliseconds of latency (single digits of milliseconds in the best case) if data is placed in the page cache. Otherwise, latency is mostly dominated by the number of seeks. If you use rotating disk drives, for a system that is not overloaded, the latency can be estimated with this formula: seek time (10 ms) * count of columns queried * count of data parts.

#### Throughput When Processing a Large Quantity of Short Queries 

Under the same conditions, ClickHouse can handle several hundred queries per second on a single server (up to several thousand in the best case). Since this scenario is not typical for analytical DBMSs, we recommend expecting a maximum of 100 queries per second.

#### Performance When Inserting Data 

*We recommend inserting data in packets of at least 1000 rows, or no more than a single request per second.* When inserting to a MergeTree table from a tab-separated dump, the insertion speed can be from 50 to 200 MB/s. If the inserted rows are around 1 KB in size, the speed will be from 50,000 to 200,000 rows per second. If the rows are small, the performance can be higher in rows per second (on Banner System data -> 500,000 rows per second; on Graphite data -> 1,000,000 rows per second). To improve performance, you can make multiple INSERT queries in parallel, which scales linearly.

## Getting Started

### Example Datasets

### Installation

#### System Requirements

ClickHouse can run on any Linux, FreeBSD, or Mac OS X with x86_64, AArch64, or PowerPC64LE CPU architecture.

Official pre-built binaries are typically compiled for x86_64 and leverage SSE 4.2 instruction set, so unless otherwise stated usage of CPU that supports it becomes an additional system requirement. Here's the command to check if current CPU has support for SSE 4.2:

```shell
$ grep -q sse4_2 /proc/cpuinfo && echo "SSE 4.2 supported" || echo "SSE 4.2 not supported"
```

To run ClickHouse on processors that do not support SSE 4.2 or have AArch64 or PowerPC64LE architecture, you should build ClickHouse from sources with proper configuration adjustments.

#### Available Installation Options

##### From DEB Packages 

It is recommended to use official pre-compiled deb packages for Debian or Ubuntu. Run these commands to install packages:

```shell
sudo apt-get install apt-transport-https ca-certificates dirmngr
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv E0C56BD4

echo "deb https://repo.clickhouse.tech/deb/stable/ main/" | sudo tee \
    /etc/apt/sources.list.d/clickhouse.list
sudo apt-get update

sudo apt-get install -y clickhouse-server clickhouse-client

sudo service clickhouse-server start
clickhouse-client
```

If you want to use the most recent version, replace stable with testing (this is recommended for your testing environments).

You can also download and install packages manually from here.

Packages 
- clickhouse-common-static — Installs ClickHouse compiled binary files.
- clickhouse-server — Creates a symbolic link for clickhouse-server and installs the default server configuration.
- clickhouse-client — Creates a symbolic link for clickhouse-client and other client-related tools. and installs client configuration files.
- clickhouse-common-static-dbg — Installs ClickHouse compiled binary files with debug info.

##### Launch

To start the server as a daemon, run:

```shell
$ sudo service clickhouse-server start
```

If you don't have service command, run as

```shell
$ sudo /etc/init.d/clickhouse-server start
```

See the logs in the `/var/log/clickhouse-server/` directory.

If the server doesn't start, check the configurations in the file /etc/clickhouse-server/config.xml.

You can also manually launch the server from the console:

```shell
$ clickhouse-server --config-file=/etc/clickhouse-server/config.xml
```

In this case, the log will be printed to the console, which is convenient during development.

If the configuration file is in the current directory, you don't need to specify the `--config-file` parameter. By default, it uses `./config`.xml.

ClickHouse supports access restriction settings. They are located in the users.xml file (next to config.xml).

By default, access is allowed from anywhere for the default user, without a password. See `user/default/networks`.

For more information, see the section "Configuration Files".

After launching server, you can use the command-line client to connect to it:

```shell
$ clickhouse-client
```

By default, it connects to `localhost:9000` on behalf of the user default without a password. It can also be used to connect to a remote server using `--host` argument.

The terminal must use UTF-8 encoding.
For more information, see the section "Command-line client".

Example:

```
$ ./clickhouse-client
ClickHouse client version 0.0.18749.
Connecting to localhost:9000.
Connected to ClickHouse server version 0.0.18749.

:) SELECT 1

SELECT 1

┌─1─┐
│ 1 │
└───┘

1 rows in set. Elapsed: 0.003 sec.

:)
```

### Tutorial

#### What to Expect from This Tutorial?

By going through this tutorial, you'll learn how to set up a simple ClickHouse cluster. It'll be small, but fault-tolerant and scalable. Then we will use one of the example datasets to fill it with data and execute some demo queries.

#### Single Node Setup

To postpone the complexities of a distributed environment, we'll start with deploying ClickHouse on a single server or virtual machine. ClickHouse is usually installed from deb or rpm packages, but there are alternatives for the operating systems that do not support them.

For example, you have chosen deb packages and executed:

```shell
sudo apt-get install apt-transport-https ca-certificates dirmngr
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv E0C56BD4

echo "deb https://repo.clickhouse.tech/deb/stable/ main/" | sudo tee \
    /etc/apt/sources.list.d/clickhouse.list
sudo apt-get update

sudo apt-get install -y clickhouse-server clickhouse-client

sudo service clickhouse-server start
clickhouse-client
```

What do we have in the packages that got installed:

- clickhouse-client package contains clickhouse-client application, interactive ClickHouse console client.
- clickhouse-common package contains a ClickHouse executable file.
- clickhouse-server package contains configuration files to run ClickHouse as a server.

Server config files are located in `/etc/clickhouse-server/`. Before going further, please notice the `<path>` element in `config.xml`. *Path determines the location for data storage, so it should be located on volume with large disk capacity*; the default value is `/var/lib/clickhouse/`. If you want to adjust the configuration, it's not handy to directly edit `config.xml` file, considering it might get rewritten on future package updates. The recommended way to override the config elements is to create files in `config.d` directory which serve as "patches" to `config.xml`.

As you might have noticed, *clickhouse-server is not launched automatically after package installation*. It won't be automatically restarted after updates, either. The way you start the server depends on your init system, usually, it is:

```shell
sudo service clickhouse-server start
```

or

```shell
sudo /etc/init.d/clickhouse-server start
```

The default location for server logs is `/var/log/clickhouse-server/`. The server is ready to handle client connections once it logs the Ready for connections message.

Once the clickhouse-server is up and running, we can use clickhouse-client to connect to the server and run some test queries like `SELECT "Hello, world!";`.

#### Import Sample Dataset

Now it's time to fill our ClickHouse server with some sample data. In this tutorial, we'll use the anonymized data of Yandex.Metrica, the first service that runs ClickHouse in production way before it became open-source (more on that in history section). There are multiple ways to import Yandex.Metrica dataset, and for the sake of the tutorial, we'll go with the most realistic one.

##### Download and Extract Table Data

```shell
curl https://datasets.clickhouse.tech/hits/tsv/hits_v1.tsv.xz | unxz --threads=`nproc` > hits_v1.tsv
curl https://datasets.clickhouse.tech/visits/tsv/visits_v1.tsv.xz | unxz --threads=`nproc` > visits_v1.tsv
```

The extracted files are about 10GB in size.

##### Create Tables 

As in most databases management systems, ClickHouse logically groups tables into "databases". There's a default database, but we'll create a new one named `tutorial`:

```shell
clickhouse-client --query "CREATE DATABASE IF NOT EXISTS tutorial"
```

Syntax for creating tables is way more complicated compared to databases. In general `CREATE TABLE` statement has to specify three key things:

1. Name of table to create.
2. Table schema, i.e. list of columns and their data types.
3. Table engine and its settings, which determines all the details on how queries to this table will be physically executed.

Yandex.Metrica is a web analytics service, and sample dataset doesn't cover its full functionality, so there are only two tables to create:

- `hits` is a table with each action done by all users on all websites covered by the service.
- `visits` is a table that contains pre-built sessions instead of individual actions.

Let's see and execute the real create table queries for these tables:

```sql
CREATE TABLE tutorial.hits_v1
(
    `WatchID` UInt64,
    `JavaEnable` UInt8,
    `Title` String,
    `GoodEvent` Int16,
    `EventTime` DateTime,
    `EventDate` Date,
    `CounterID` UInt32,
    `ClientIP` UInt32,
    `ClientIP6` FixedString(16),
    `RegionID` UInt32,
    `UserID` UInt64,
    `CounterClass` Int8,
    `OS` UInt8,
    `UserAgent` UInt8,
    `URL` String,
    `Referer` String,
    `URLDomain` String,
    `RefererDomain` String,
    `Refresh` UInt8,
    `IsRobot` UInt8,
    `RefererCategories` Array(UInt16),
    `URLCategories` Array(UInt16),
    `URLRegions` Array(UInt32),
    `RefererRegions` Array(UInt32),
    `ResolutionWidth` UInt16,
    `ResolutionHeight` UInt16,
    `ResolutionDepth` UInt8,
    `FlashMajor` UInt8,
    `FlashMinor` UInt8,
    `FlashMinor2` String,
    `NetMajor` UInt8,
    `NetMinor` UInt8,
    `UserAgentMajor` UInt16,
    `UserAgentMinor` FixedString(2),
    `CookieEnable` UInt8,
    `JavascriptEnable` UInt8,
    `IsMobile` UInt8,
    `MobilePhone` UInt8,
    `MobilePhoneModel` String,
    `Params` String,
    `IPNetworkID` UInt32,
    `TraficSourceID` Int8,
    `SearchEngineID` UInt16,
    `SearchPhrase` String,
    `AdvEngineID` UInt8,
    `IsArtifical` UInt8,
    `WindowClientWidth` UInt16,
    `WindowClientHeight` UInt16,
    `ClientTimeZone` Int16,
    `ClientEventTime` DateTime,
    `SilverlightVersion1` UInt8,
    `SilverlightVersion2` UInt8,
    `SilverlightVersion3` UInt32,
    `SilverlightVersion4` UInt16,
    `PageCharset` String,
    `CodeVersion` UInt32,
    `IsLink` UInt8,
    `IsDownload` UInt8,
    `IsNotBounce` UInt8,
    `FUniqID` UInt64,
    `HID` UInt32,
    `IsOldCounter` UInt8,
    `IsEvent` UInt8,
    `IsParameter` UInt8,
    `DontCountHits` UInt8,
    `WithHash` UInt8,
    `HitColor` FixedString(1),
    `UTCEventTime` DateTime,
    `Age` UInt8,
    `Sex` UInt8,
    `Income` UInt8,
    `Interests` UInt16,
    `Robotness` UInt8,
    `GeneralInterests` Array(UInt16),
    `RemoteIP` UInt32,
    `RemoteIP6` FixedString(16),
    `WindowName` Int32,
    `OpenerName` Int32,
    `HistoryLength` Int16,
    `BrowserLanguage` FixedString(2),
    `BrowserCountry` FixedString(2),
    `SocialNetwork` String,
    `SocialAction` String,
    `HTTPError` UInt16,
    `SendTiming` Int32,
    `DNSTiming` Int32,
    `ConnectTiming` Int32,
    `ResponseStartTiming` Int32,
    `ResponseEndTiming` Int32,
    `FetchTiming` Int32,
    `RedirectTiming` Int32,
    `DOMInteractiveTiming` Int32,
    `DOMContentLoadedTiming` Int32,
    `DOMCompleteTiming` Int32,
    `LoadEventStartTiming` Int32,
    `LoadEventEndTiming` Int32,
    `NSToDOMContentLoadedTiming` Int32,
    `FirstPaintTiming` Int32,
    `RedirectCount` Int8,
    `SocialSourceNetworkID` UInt8,
    `SocialSourcePage` String,
    `ParamPrice` Int64,
    `ParamOrderID` String,
    `ParamCurrency` FixedString(3),
    `ParamCurrencyID` UInt16,
    `GoalsReached` Array(UInt32),
    `OpenstatServiceName` String,
    `OpenstatCampaignID` String,
    `OpenstatAdID` String,
    `OpenstatSourceID` String,
    `UTMSource` String,
    `UTMMedium` String,
    `UTMCampaign` String,
    `UTMContent` String,
    `UTMTerm` String,
    `FromTag` String,
    `HasGCLID` UInt8,
    `RefererHash` UInt64,
    `URLHash` UInt64,
    `CLID` UInt32,
    `YCLID` UInt64,
    `ShareService` String,
    `ShareURL` String,
    `ShareTitle` String,
    `ParsedParams` Nested(
        Key1 String,
        Key2 String,
        Key3 String,
        Key4 String,
        Key5 String,
        ValueDouble Float64),
    `IslandID` FixedString(16),
    `RequestNum` UInt32,
    `RequestTry` UInt8
)
ENGINE = MergeTree()
PARTITION BY toYYYYMM(EventDate)
ORDER BY (CounterID, EventDate, intHash32(UserID))
SAMPLE BY intHash32(UserID)
```

> Here intHash32(UserID) is used for ordering key (primary key). How to avoid hash collision?

```sql
CREATE TABLE tutorial.visits_v1
(
    `CounterID` UInt32,
    `StartDate` Date,
    `Sign` Int8,
    `IsNew` UInt8,
    `VisitID` UInt64,
    `UserID` UInt64,
    `StartTime` DateTime,
    `Duration` UInt32,
    `UTCStartTime` DateTime,
    `PageViews` Int32,
    `Hits` Int32,
    `IsBounce` UInt8,
    `Referer` String,
    `StartURL` String,
    `RefererDomain` String,
    `StartURLDomain` String,
    `EndURL` String,
    `LinkURL` String,
    `IsDownload` UInt8,
    `TraficSourceID` Int8,
    `SearchEngineID` UInt16,
    `SearchPhrase` String,
    `AdvEngineID` UInt8,
    `PlaceID` Int32,
    `RefererCategories` Array(UInt16),
    `URLCategories` Array(UInt16),
    `URLRegions` Array(UInt32),
    `RefererRegions` Array(UInt32),
    `IsYandex` UInt8,
    `GoalReachesDepth` Int32,
    `GoalReachesURL` Int32,
    `GoalReachesAny` Int32,
    `SocialSourceNetworkID` UInt8,
    `SocialSourcePage` String,
    `MobilePhoneModel` String,
    `ClientEventTime` DateTime,
    `RegionID` UInt32,
    `ClientIP` UInt32,
    `ClientIP6` FixedString(16),
    `RemoteIP` UInt32,
    `RemoteIP6` FixedString(16),
    `IPNetworkID` UInt32,
    `SilverlightVersion3` UInt32,
    `CodeVersion` UInt32,
    `ResolutionWidth` UInt16,
    `ResolutionHeight` UInt16,
    `UserAgentMajor` UInt16,
    `UserAgentMinor` UInt16,
    `WindowClientWidth` UInt16,
    `WindowClientHeight` UInt16,
    `SilverlightVersion2` UInt8,
    `SilverlightVersion4` UInt16,
    `FlashVersion3` UInt16,
    `FlashVersion4` UInt16,
    `ClientTimeZone` Int16,
    `OS` UInt8,
    `UserAgent` UInt8,
    `ResolutionDepth` UInt8,
    `FlashMajor` UInt8,
    `FlashMinor` UInt8,
    `NetMajor` UInt8,
    `NetMinor` UInt8,
    `MobilePhone` UInt8,
    `SilverlightVersion1` UInt8,
    `Age` UInt8,
    `Sex` UInt8,
    `Income` UInt8,
    `JavaEnable` UInt8,
    `CookieEnable` UInt8,
    `JavascriptEnable` UInt8,
    `IsMobile` UInt8,
    `BrowserLanguage` UInt16,
    `BrowserCountry` UInt16,
    `Interests` UInt16,
    `Robotness` UInt8,
    `GeneralInterests` Array(UInt16),
    `Params` Array(String),
    `Goals` Nested(
        ID UInt32,
        Serial UInt32,
        EventTime DateTime,
        Price Int64,
        OrderID String,
        CurrencyID UInt32),
    `WatchIDs` Array(UInt64),
    `ParamSumPrice` Int64,
    `ParamCurrency` FixedString(3),
    `ParamCurrencyID` UInt16,
    `ClickLogID` UInt64,
    `ClickEventID` Int32,
    `ClickGoodEvent` Int32,
    `ClickEventTime` DateTime,
    `ClickPriorityID` Int32,
    `ClickPhraseID` Int32,
    `ClickPageID` Int32,
    `ClickPlaceID` Int32,
    `ClickTypeID` Int32,
    `ClickResourceID` Int32,
    `ClickCost` UInt32,
    `ClickClientIP` UInt32,
    `ClickDomainID` UInt32,
    `ClickURL` String,
    `ClickAttempt` UInt8,
    `ClickOrderID` UInt32,
    `ClickBannerID` UInt32,
    `ClickMarketCategoryID` UInt32,
    `ClickMarketPP` UInt32,
    `ClickMarketCategoryName` String,
    `ClickMarketPPName` String,
    `ClickAWAPSCampaignName` String,
    `ClickPageName` String,
    `ClickTargetType` UInt16,
    `ClickTargetPhraseID` UInt64,
    `ClickContextType` UInt8,
    `ClickSelectType` Int8,
    `ClickOptions` String,
    `ClickGroupBannerID` Int32,
    `OpenstatServiceName` String,
    `OpenstatCampaignID` String,
    `OpenstatAdID` String,
    `OpenstatSourceID` String,
    `UTMSource` String,
    `UTMMedium` String,
    `UTMCampaign` String,
    `UTMContent` String,
    `UTMTerm` String,
    `FromTag` String,
    `HasGCLID` UInt8,
    `FirstVisit` DateTime,
    `PredLastVisit` Date,
    `LastVisit` Date,
    `TotalVisits` UInt32,
    `TraficSource` Nested(
        ID Int8,
        SearchEngineID UInt16,
        AdvEngineID UInt8,
        PlaceID UInt16,
        SocialSourceNetworkID UInt8,
        Domain String,
        SearchPhrase String,
        SocialSourcePage String),
    `Attendance` FixedString(16),
    `CLID` UInt32,
    `YCLID` UInt64,
    `NormalizedRefererHash` UInt64,
    `SearchPhraseHash` UInt64,
    `RefererDomainHash` UInt64,
    `NormalizedStartURLHash` UInt64,
    `StartURLDomainHash` UInt64,
    `NormalizedEndURLHash` UInt64,
    `TopLevelDomain` UInt64,
    `URLScheme` UInt64,
    `OpenstatServiceNameHash` UInt64,
    `OpenstatCampaignIDHash` UInt64,
    `OpenstatAdIDHash` UInt64,
    `OpenstatSourceIDHash` UInt64,
    `UTMSourceHash` UInt64,
    `UTMMediumHash` UInt64,
    `UTMCampaignHash` UInt64,
    `UTMContentHash` UInt64,
    `UTMTermHash` UInt64,
    `FromHash` UInt64,
    `WebVisorEnabled` UInt8,
    `WebVisorActivity` UInt32,
    `ParsedParams` Nested(
        Key1 String,
        Key2 String,
        Key3 String,
        Key4 String,
        Key5 String,
        ValueDouble Float64),
    `Market` Nested(
        Type UInt8,
        GoalID UInt32,
        OrderID String,
        OrderPrice Int64,
        PP UInt32,
        DirectPlaceID UInt32,
        DirectOrderID UInt32,
        DirectBannerID UInt32,
        GoodID String,
        GoodName String,
        GoodQuantity Int32,
        GoodPrice Int64),
    `IslandID` FixedString(16)
)
ENGINE = CollapsingMergeTree(Sign)
PARTITION BY toYYYYMM(StartDate)
ORDER BY (CounterID, StartDate, intHash32(UserID), VisitID)
SAMPLE BY intHash32(UserID)
```

> What is CollapsingMergeTree?

You can execute those queries using the interactive mode of `clickhouse-client` (just launch it in a terminal without specifying a query in advance) or try some alternative interface if you want.

As we can see, `hits_v1` uses the basic MergeTree engine, while the `visits_v1` uses the Collapsing variant.

##### Import Data

Data import to ClickHouse is done via `INSERT INTO` query like in many other SQL databases. However, *data is usually provided in one of the supported serialization formats* instead of `VALUES` clause (which is also supported).

The files we downloaded earlier are in tab-separated format, so here's how to import them via console client:

```shell
clickhouse-client --query "INSERT INTO tutorial.hits_v1 FORMAT TSV" --max_insert_block_size=100000 < hits_v1.tsv
clickhouse-client --query "INSERT INTO tutorial.visits_v1 FORMAT TSV" --max_insert_block_size=100000 < visits_v1.tsv
```

ClickHouse has a lot of settings to tune and one way to specify them in console client is via arguments, as we can see with `--max_insert_block_size`. The easiest way to figure out what settings are available, what do they mean and what the defaults are is to query the system.settings table:

```sql
SELECT name, value, changed, description
FROM system.settings
WHERE name LIKE '%max_insert_b%'
FORMAT TSV

max_insert_block_size    1048576    0    "The maximum block size for insertion, if we control the creation of blocks for insertion."
```

Optionally you can `OPTIMIZE` the tables after import. Tables that are configured with an engine from MergeTree-family always do merges of data parts in the background to optimize data storage (or at least check if it makes sense). *These queries force the table engine to do storage optimization right now instead of some time later*:

```shell
clickhouse-client --query "OPTIMIZE TABLE tutorial.hits_v1 FINAL"
clickhouse-client --query "OPTIMIZE TABLE tutorial.visits_v1 FINAL"
```

These queries start an I/O and CPU intensive operation, so if the table consistently receives new data, it's better to leave it alone and let merges run in the background.

Now we can check if the table import was successful:

```shell
clickhouse-client --query "SELECT COUNT(*) FROM tutorial.hits_v1"
clickhouse-client --query "SELECT COUNT(*) FROM tutorial.visits_v1"
```

## Interfaces

ClickHouse provides two network interfaces (both can be optionally wrapped in TLS for additional security):

- HTTP, which is documented and easy to use directly.
- Native TCP, which has less overhead.
  
In most cases it is recommended to use appropriate tool or library instead of interacting with those directly. Officially supported by Yandex are the following:

- Command-line client
- JDBC driver
- ODBC driver
- C++ client library

There are also a wide range of third-party libraries for working with ClickHouse:

- Client libraries
- Integrations
- Visual interfaces

### Command-line Client

ClickHouse provides a native command-line client: `clickhouse-client`. The client supports command-line options and configuration files. For more information, see Configuring.

Install it from the `clickhouse-client` package and run it with the command `clickhouse-client`.

```shell
$ clickhouse-client
ClickHouse client version 20.13.1.5273 (official build).
Connecting to localhost:9000 as user default.
Connected to ClickHouse server version 20.13.1 revision 54442.

:)
```

Different client and server versions are compatible with one another, but some features may not be available in older clients. We recommend using the same version of the client as the server app. When you try to use a client of the older version, then the server, clickhouse-client displays the message:

```
ClickHouse client version is older than ClickHouse server. It may lack support for new features.
```

#### Usage 

The client can be used in interactive and non-interactive (batch) mode. To use batch mode, specify the 'query' parameter, or send data to 'stdin' (it verifies that 'stdin' is not a terminal), or both. Similar to the HTTP interface, when using the 'query' parameter and sending data to 'stdin', the request is a concatenation of the 'query' parameter, a line feed, and the data in 'stdin'. This is convenient for large INSERT queries.

Example of using the client to insert data:

```shell
$ echo -ne "1, 'some text', '2016-08-14 00:00:00'\n2, 'some more text', '2016-08-14 00:00:01'" | clickhouse-client --database=test --query="INSERT INTO test FORMAT CSV";

$ cat <<_EOF | clickhouse-client --database=test --query="INSERT INTO test FORMAT CSV";
3, 'some text', '2016-08-14 00:00:00'
4, 'some more text', '2016-08-14 00:00:01'
_EOF

$ cat file.csv | clickhouse-client --database=test --query="INSERT INTO test FORMAT CSV";
```

In batch mode, the default data format is `TabSeparated`. You can set the format in the `FORMAT` clause of the query.

By default, you can only process a single query in batch mode. To make multiple queries from a "script," use the `--multiquery` parameter. This works for all queries except `INSERT`. Query results are output consecutively without additional separators. Similarly, to process a large number of queries, you can run clickhouse-client for each query. Note that it may take tens of milliseconds to launch the 'clickhouse-client' program.

In interactive mode, you get a command line where you can enter queries.

If 'multiline' is not specified (the default): To run the query, press Enter. The semicolon is not necessary at the end of the query. To enter a multiline query, enter a backslash \ before the line feed. After you press Enter, you will be asked to enter the next line of the query.

If multiline is specified: To run a query, end it with a semicolon and press Enter. If the semicolon was omitted at the end of the entered line, you will be asked to enter the next line of the query.

Only a single query is run, so everything after the semicolon is ignored.

You can specify \G instead of or after the semicolon. This indicates Vertical format. In this format, each value is printed on a separate line, which is convenient for wide tables. This unusual feature was added for compatibility with the MySQL CLI.

The command line is based on 'replxx' (similar to 'readline'). In other words, it uses the familiar keyboard shortcuts and keeps a history. The history is written to ~/.clickhouse-client-history.

By default, the format used is PrettyCompact. You can change the format in the FORMAT clause of the query, or by specifying \G at the end of the query, using the --format or --vertical argument in the command line, or using the client configuration file.

To exit the client, press Ctrl+D, or enter one of the following instead of a query: "exit", "quit", "logout", "exit;", "quit;", "logout;", "q", "Q", ":q"

When processing a query, the client shows:

1. Progress, which is updated no more than 10 times per second (by default). For quick queries, the progress might not have time to be displayed.
2. The formatted query after parsing, for debugging.
3. The result in the specified format.
4. The number of lines in the result, the time passed, and the average speed of query processing.

You can cancel a long query by pressing Ctrl+C. However, you will still need to wait for a little for the server to abort the request. It is not possible to cancel a query at certain stages. If you don't wait and press Ctrl+C a second time, the client will exit.

The command-line client allows passing external data (external temporary tables) for querying. For more information, see the section "External data for query processing".

##### Queries with Parameters 

You can create a query with parameters and pass values to them from client application. This allows to avoid formatting query with specific dynamic values on client side. For example:

```shell
$ clickhouse-client --param_parName="[1, 2]"  -q "SELECT * FROM table WHERE a = {parName:Array(UInt16)}"
```

##### Query Syntax 
Format a query as usual, then place the values that you want to pass from the app parameters to the query in braces in the following format:

```shell
{<name>:<data type>}
```
- name — Placeholder identifier. In the console client it should be used in app parameters as `--param_<name> = value`.
- data type — Data type of the app parameter value. For example, a data structure like (integer, ('string', integer)) can have the Tuple(UInt8, Tuple(String, UInt8)) data type (you can also use another integer types). It's also possible to pass table, database, column names as a parameter, in that case you would need to use Identifier as a data type.

##### Example 

```shell
$ clickhouse-client --param_tuple_in_tuple="(10, ('dt', 10))" -q "SELECT * FROM table WHERE val = {tuple_in_tuple:Tuple(UInt8, Tuple(String, UInt8))}"
$ clickhouse-client --param_tbl="numbers" --param_db="system" --param_col="number" --query "SELECT {col:Identifier} FROM {db:Identifier}.{tbl:Identifier} LIMIT 10"
```

##### Configuring 

You can pass parameters to `clickhouse-client` (all parameters have a default value) using:

- From the Command Line

Command-line options override the default values and settings in configuration files.

- Configuration files.

Settings in the configuration files override the default values.

- Command Line Options 

- `--host, -h` – The server name, 'localhost' by default. You can use either the name or the IPv4 or IPv6 address.
- -`-port` – The port to connect to. Default value: 9000. Note that the HTTP interface and the native interface use different ports.
- `--user, -u` – The username. Default value: default.
- `--password` – The password. Default value: empty string.
- `--query, -q` – The query to process when using non-interactive mode. You must specify either query or queries-file option.
- `--queries-file, -qf` – file path with queries to execute. You must specify either query or queries-file option.
- `--database, -d` – Select the current default database. Default value: the current database from the server settings ('default' by default).
- `--multiline, -m` – If specified, allow multiline queries (do not send the query on Enter).
- `--multiquery, -n` – If specified, allow processing multiple queries separated by semicolons.
- `--format, -f` – Use the specified default format to output the result.
- `--vertical, -E` – If specified, use the Vertical format by default to output the result. This is the same as –format=Vertical. In this format, each value is printed on a separate line, which is helpful when displaying wide tables.
- `--time, -t` – If specified, print the query execution time to 'stderr' in non-interactive mode.
- `--stacktrace` – If specified, also print the stack trace if an exception occurs.
- `--config-file` – The name of the configuration file.
- `--secure` – If specified, will connect to server over secure connection.
- `--history_file` — Path to a file containing command history.
- `--param_<name>` — Value for a query with parameters.

Since version 20.5, clickhouse-client has automatic syntax highlighting (always enabled).

##### Configuration Files 

`clickhouse-client` uses the first existing file of the following:

- Defined in the `--config-file` parameter.
- `./clickhouse-client.xml`
- `~/.clickhouse-client/config.xml`
- `/etc/clickhouse-client/config.xml`

Example of a config file:

```xml
￼<config>
    <user>username</user>
    <password>password</password>
    <secure>False</secure>
</config>
```

### Native Interface (TCP)

The native protocol is used in the command-line client, for inter-server communication during distributed query processing, and also in other C++ programs. Unfortunately, native ClickHouse protocol does not have formal specification yet, but it can be reverse-engineered from ClickHouse source code (starting around here) and/or by intercepting and analyzing TCP traffic.

### HTTP Interface

## Engines

### Table Engines

The table engine (type of table) determines:

- How and where data is stored, where to write it to, and where to read it from.
- Which queries are supported, and how.
- Concurrent data access.
- Use of indexes, if present.
- Whether multithreaded request execution is possible.
- Data replication parameters.

#### Engine Families

##### MergeTree 

The most universal and functional table engines for high-load tasks. The property shared by these engines is quick data insertion with subsequent background data processing. MergeTree family engines support data replication (with Replicated* versions of engines), partitioning, secondary data-skipping indexes, and other features not supported in other engines.

Engines in the family:

- MergeTree
- ReplacingMergeTree
- SummingMergeTree
- AggregatingMergeTree
- CollapsingMergeTree
- VersionedCollapsingMergeTree
- GraphiteMergeTree

##### Log 

Lightweight engines with minimum functionality. They're the most effective when you need to quickly write many small tables (up to approximately 1 million rows) and read them later as a whole.

Engines in the family:

- TinyLog
- StripeLog
- Log

##### Integration Engines 

Engines for communicating with other data storage and processing systems.

Engines in the family:

- Kafka
- MySQL
- ODBC
- JDBC
- HDFS
- S3

##### Special Engines 

Engines in the family:

- Distributed
- MaterializedView
- Dictionary
- Merge
- File
- Null
- Set
- Join
- URL
- View
- Memory
- Buffer

##### Virtual Columns

Virtual column is an integral table engine attribute that is defined in the engine source code.

You shouldn't specify virtual columns in the `CREATE TABLE` query and you can't see them in SHOW `CREATE TABLE` and `DESCRIBE TABLE` query results. Virtual columns are also read-only, so you can't insert data into virtual columns.

To select data from a virtual column, you must specify its name in the `SELECT` query. `SELECT` * doesn't return values from virtual columns.

If you create a table with a column that has the same name as one of the table virtual columns, the virtual column becomes inaccessible. We don't recommend doing this. To help avoid conflicts, virtual column names are usually prefixed with an underscore.

### MergeTree Engine Family 

Table engines from the MergeTree family are the core of ClickHouse data storage capabilities. They provide most features for resilience and high-performance data retrieval: columnar storage, custom partitioning, sparse primary index, secondary data-skipping indexes, etc.

Base MergeTree table engine can be considered the default table engine for single-node ClickHouse instances because it is versatile and practical for a wide range of use cases.

For production usage ReplicatedMergeTree is the way to go, because it adds high-availability to all features of regular MergeTree engine. A bonus is automatic data deduplication on data ingestion, so the software can safely retry if there was some network issue during insert.

All other engines of MergeTree family add extra functionality for some specific use cases. Usually, it's implemented as additional data manipulation in background.

The main downside of MergeTree engines is that they are rather heavy-weight. So the typical pattern is to have not so many of them. If you need many small tables, for example for temporary data, consider Log engine family.

### MergeTree 

The MergeTree engine and other engines of this family (`*MergeTree`) are the most robust ClickHouse table engines.

Engines in the MergeTree family are designed for inserting a very large amount of data into a table. The data is quickly written to the table part by part, then rules are applied for merging the parts in the background. This method is much more efficient than continually rewriting the data in storage during insert.

Main features:

- Stores data sorted by primary key.

- This allows you to create a small sparse index that helps find data faster.

- Partitions can be used if the partitioning key is specified.
  
  ClickHouse supports certain operations with partitions that are more effective than general operations on the same data with the same result. ClickHouse also automatically cuts off the partition data where the partitioning key is specified in the query. This also improves query performance.

- Data replication support.

- The family of ReplicatedMergeTree tables provides data replication. For more information, see Data replication.

- Data sampling support.
  If necessary, you can set the data sampling method in the table.

The Merge engine does not belong to the `*MergeTree` family.

#### Creating a Table

```sql
CREATE TABLE [IF NOT EXISTS] [db.]table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT|MATERIALIZED|ALIAS expr1] [TTL expr1],
    name2 [type2] [DEFAULT|MATERIALIZED|ALIAS expr2] [TTL expr2],
    ...
    INDEX index_name1 expr1 TYPE type1(...) GRANULARITY value1,
    INDEX index_name2 expr2 TYPE type2(...) GRANULARITY value2
) ENGINE = MergeTree()
ORDER BY expr
[PARTITION BY expr]
[PRIMARY KEY expr]
[SAMPLE BY expr]
[TTL expr 
    [DELETE|TO DISK 'xxx'|TO VOLUME 'xxx' [, ...] ]
    [WHERE conditions] 
    [GROUP BY key_expr [SET v1 = aggr_func(v1) [, v2 = aggr_func(v2) ...]] ] ] 
[SETTINGS name=value, ...]
```

For a description of parameters, see the CREATE query description.

##### Query Clauses

- `ENGINE` — Name and parameters of the engine. `ENGINE = MergeTree()`. The MergeTree engine does not have parameters.

- `ORDER BY` — The sorting key.

- A tuple of column names or arbitrary expressions. Example: `ORDER BY (CounterID, EventDate)`.

  ClickHouse uses the sorting key as a primary key if the primary key is not defined obviously by the `PRIMARY KEY` clause.

  Use the `ORDER BY` tuple() syntax, if you don't need sorting. See Selecting the Primary Key.

  > If the sorting key is defined by tuple() syntax, no data sorting happens?

- `PARTITION BY` — The partitioning key. Optional.

  For partitioning by month, use the `toYYYYMM(date_column)` expression, where `date_column` is a column with a date of the type Date. The partition names here have the "YYYYMM" format.

- `PRIMARY KEY` — The primary key if it differs from the sorting key. Optional.

  By default the primary key is the same as the sorting key (which is specified by the `ORDER BY` clause). Thus in most cases it is unnecessary to specify a separate `PRIMARY KEY` clause.

- `SAMPLE BY` — An expression for sampling. Optional.

  If a sampling expression is used, the primary key must contain it. Example: `SAMPLE BY intHash32(UserID) ORDER BY (CounterID, EventDate, intHash32(UserID))`.

- *`TTL` — A list of rules specifying storage duration of rows and defining logic of automatic parts movement between disks and volumes.* Optional.

  Expression must have one `Date` or `DateTime` column as a result. Example: `TTL date + INTERVAL 1 DAY`

  Type of the rule `DELETE|TO DISK 'xxx'|TO VOLUME 'xxx'|GROUP BY` specifies an action to be done with the part if the expression is satisfied (reaches current time): removal of expired rows, moving a part (if expression is satisfied for all rows in a part) to specified disk (`TO DISK` 'xxx') or to volume (`TO VOLUME` 'xxx'), or aggregating values in expired rows. Default type of the rule is removal (`DELETE`). List of multiple rules can specified, but there should be no more than one `DELETE` rule.

  For more details, see TTL for columns and tables

- `SETTINGS` — Additional parameters that control the behavior of the MergeTree (optional):

  - `index_granularity` — Maximum number of data rows between the marks of an index. Default value: 8192. See Data Storage.
  - `index_granularity_bytes` — Maximum size of data granules in bytes. Default value: 10Mb. To restrict the granule size only by number of rows, set to 0 (not recommended). See Data Storage.
  - `min_index_granularity_bytes` — Min allowed size of data granules in bytes. Default value: 1024b. To provide a safeguard against accidentally creating tables with very low `index_granularity_bytes`. See Data Storage.
  - `enable_mixed_granularity_parts` — Enables or disables transitioning to control the granule size with the `index_granularity_bytes` setting. Before version 19.11, there was only the `index_granularity` setting for restricting granule size. The `index_granularity_bytes` setting improves ClickHouse performance when selecting data from tables with big rows (tens and hundreds of megabytes). If you have tables with big rows, you can enable this setting for the tables to improve the efficiency of SELECT queries.
  - `use_minimalistic_part_header_in_zookeeper` — Storage method of the data parts headers in ZooKeeper. If `use_minimalistic_part_header_in_zookeeper=1`, then ZooKeeper stores less data. For more information, see the setting description in "Server configuration parameters".
  - `min_merge_bytes_to_use_direct_io` — The minimum data volume for merge operation that is required for using direct I/O access to the storage disk. When merging data parts, ClickHouse calculates the total storage volume of all the data to be merged. If the volume exceeds `min_merge_bytes_to_use_direct_io` bytes, ClickHouse reads and writes the data to the storage disk using the direct I/O interface (`O_DIRECT` option). If `min_merge_bytes_to_use_direct_io = 0`, then direct I/O is disabled. Default value: 10 * 1024 * 1024 * 1024 bytes.
  - `merge_with_ttl_timeout` — Minimum delay in seconds before repeating a merge with TTL. Default value: 86400 (1 day).
  - `write_final_mark` — Enables or disables writing the final index mark at the end of data part (after the last byte). Default value: 1. Don't turn it off.
  - `merge_max_block_size` — Maximum number of rows in block for merge operations. Default value: 8192.
  - `storage_policy` — Storage policy. See Using Multiple Block Devices for Data Storage.
  - `min_bytes_for_wide_part`, `min_rows_for_wide_part` — Minimum number of bytes/rows in a data part that can be stored in Wide format. You can set one, both or none of these settings. See Data Storage.
  - `max_parts_in_total` — Maximum number of parts in all partitions.
  - `max_compress_block_size` — Maximum size of blocks of uncompressed data before compressing for writing to a table. You can also specify this setting in the global settings (see max_compress_block_size setting). The value specified when table is created overrides the global value for this setting.
  - `min_compress_block_size` — Minimum size of blocks of uncompressed data required for compression when writing the next mark. You can also specify this setting in the global settings (see min_compress_block_size setting). The value specified when table is created overrides the global value for this setting.
  - `max_partitions_to_read` — Limits the maximum number of partitions that can be accessed in one query. You can also specify setting `max_partitions_to_read` in the global setting.

#### Example of Sections Setting

```sql
ENGINE MergeTree() PARTITION BY toYYYYMM(EventDate) ORDER BY (CounterID, EventDate, intHash32(UserID)) SAMPLE BY intHash32(UserID) SETTINGS index_granularity=8192
```

In the example, we set partitioning by month.

We also set an expression for sampling as a hash by the user ID. This allows you to pseudorandomize the data in the table for each CounterID and EventDate. If you define a SAMPLE clause when selecting the data, ClickHouse will return an evenly pseudorandom data sample for a subset of users.

The `index_granularity` setting can be omitted because 8192 is the default value.

#### Data Storage 

A table consists of data parts sorted by primary key.

*When data is inserted in a table, separate data parts are created and each of them is lexicographically sorted by primary key.* For example, if the primary key is (CounterID, Date), the data in the part is sorted by CounterID, and within each CounterID, it is ordered by Date.

*Data belonging to different partitions are separated into different parts. In the background, ClickHouse merges data parts for more efficient storage. Parts belonging to different partitions are not merged.* The merge mechanism does not guarantee that all rows with the same primary key will be in the same data part.

*Data parts can be stored in Wide or Compact format. In Wide format each column is stored in a separate file in a filesystem, in Compact format all columns are stored in one file. Compact format can be used to increase performance of small and frequent inserts.*

*Data storing format is controlled by the `min_bytes_for_wide_part` and `min_rows_for_wide_part` settings of the table engine. If the number of bytes or rows in a data part is less then the corresponding setting's value, the part is stored in Compact format. Otherwise it is stored in Wide format. If none of these settings is set, data parts are stored in Wide format.*

*Each data part is logically divided into granules. A granule is the smallest indivisible data set that ClickHouse reads when selecting data. ClickHouse doesn't split rows or values, so each granule always contains an integer number of rows. The first row of a granule is marked with the value of the primary key for the row. For each data part, ClickHouse creates an index file that stores the marks. For each column, whether it's in the primary key or not, ClickHouse also stores the same marks. These marks let you find data directly in column files.*

*The granule size is restricted by the `index_granularity` and `index_granularity_bytes` settings of the table engine. The number of rows in a granule lays in the `[1, index_granularity]` range, depending on the size of the rows. The size of a granule can exceed `index_granularity_bytes` if the size of a single row is greater than the value of the setting. In this case, the size of the granule equals the size of the row.*

#### Primary Keys and Indexes in Queries

Take the (CounterID, Date) primary key as an example. In this case, the sorting and index can be illustrated as follows:

```
 Whole data:     [--------------------------------------------------------------------------]
  CounterID:      [aaaaaaaaaaaaaaaaaabbbbcdeeeeeeeeeeeeefgggggggghhhhhhhhhiiiiiiiiikllllllll]
  Date:           [1111111222222233331233211111222222333211111112122222223111112223311122333]
  Marks:           |      |      |      |      |      |      |      |      |      |      |
                  a,1    a,2    a,3    b,3    e,2    e,3    g,1    h,2    i,1    i,3    l,3
  Marks numbers:   0      1      2      3      4      5      6      7      8      9      10
```

> The number of rows in a guanule (index_granualarity) is 8 in the example.

If the data query specifies:

CounterID in ('a', 'h'), the server reads the data in the ranges of marks [0, 3) and [6, 8).
CounterID IN ('a', 'h') AND Date = 3, the server reads the data in the ranges of marks [1, 3) and [7, 8).
Date = 3, the server reads the data in the range of marks [1, 10].
The examples above show that it is always more effective to use an index than a full scan.

A sparse index allows extra data to be read. When reading a single range of the primary key, up to `index_granularity * 2` extra rows in each data block can be read.

Sparse indexes allow you to work with a very large number of table rows, because in most cases, such indexes fit in the computer's RAM.

*ClickHouse does not require a unique primary key.* You can insert multiple rows with the same primary key.

You can use Nullable-typed expressions in the `PRIMARY KEY` and `ORDER BY` clauses. To allow this feature, turn on the `allow_nullable_key` setting.

The `NULLS_LAST` principle applies for `NULL` values in the `ORDER BY` clause.

#### Selecting the Primary Key

The number of columns in the primary key is not explicitly limited. Depending on the data structure, you can include more or fewer columns in the primary key. This may:

- Improve the performance of an index.
- If the primary key is `(a, b)`, then adding another column `c` will improve the performance if the following conditions are met:
  - There are queries with a condition on column `c`.
  - Long data ranges (several times longer than the `index_granularity`) with identical values for `(a, b)` are common. In other words, when adding another column allows you to skip quite long data ranges.
  - Improve data compression.

*ClickHouse sorts data by primary key, so the higher the consistency, the better the compression.*

Provide additional logic when merging data parts in the `CollapsingMergeTree` and `SummingMergeTree` engines.

In this case it makes sense to specify the sorting key that is different from the primary key.

A long primary key will negatively affect the insert performance and memory consumption, but extra columns in the primary key do not affect ClickHouse performance during `SELECT` queries.

You can create a table without a primary key using the `ORDER BY tuple()` syntax. In this case, ClickHouse stores data in the order of inserting. If you want to save data order when inserting data by `INSERT` ... `SELECT` queries, set `max_insert_threads = 1`.

> Data is not stored by the order of `ORDER BY tuple()`!

To select data in the initial order, use single-threaded `SELECT` queries.

#### Choosing a Primary Key that Differs from the Sorting Key

*It is possible to specify a primary key (an expression with values that are written in the index file for each mark) that is different from the sorting key (an expression for sorting the rows in data parts). In this case the primary key expression tuple must be a prefix of the sorting key expression tuple.*

This feature is helpful when using the `SummingMergeTree` and `AggregatingMergeTree` table engines. In a common case when using these engines, the table has two types of columns: dimensions and measures. Typical queries aggregate values of measure columns with arbitrary `GROUP BY` and filtering by dimensions. Because `SummingMergeTree` and `AggregatingMergeTree` aggregate rows with the same value of the sorting key, it is natural to add all dimensions to it. As a result, the key expression consists of a long list of columns and this list must be frequently updated with newly added dimensions.

In this case it makes sense to leave only a few columns in the primary key that will provide efficient range scans and add the remaining dimension columns to the sorting key tuple.

`ALTER` of the sorting key is a lightweight operation because when a new column is simultaneously added to the table and to the sorting key, existing data parts don't need to be changed. Since the old sorting key is a prefix of the new sorting key and there is no data in the newly added column, the data is sorted by both the old and new sorting keys at the moment of table modification.

#### Use of Indexes and Partitions in Queries

For `SELECT` queries, ClickHouse analyzes whether an index can be used. *An index can be used if the `WHERE/PREWHERE` clause has an expression (as one of the conjunction elements, or entirely) that represents an equality or inequality comparison operation, or if it has `IN` or `LIKE` with a fixed prefix on columns or expressions that are in the primary key or partitioning key, or on certain partially repetitive functions of these columns, or logical relationships of these expressions.*

Thus, it is possible to quickly run queries on one or many ranges of the primary key. In this example, queries will be fast when run for a specific tracking tag, for a specific tag and date range, for a specific tag and date, for multiple tags with a date range, and so on.

Let's look at the engine configured as follows:

```sql
ENGINE MergeTree() PARTITION BY toYYYYMM(EventDate) ORDER BY (CounterID, EventDate) SETTINGS index_granularity=8192
```

In this case, in queries:

```sql
SELECT count() FROM table WHERE EventDate = toDate(now()) AND CounterID = 34
SELECT count() FROM table WHERE EventDate = toDate(now()) AND (CounterID = 34 OR CounterID = 42)
SELECT count() FROM table WHERE ((EventDate >= toDate('2014-01-01') AND EventDate <= toDate('2014-01-31')) OR EventDate = toDate('2014-05-01')) AND CounterID IN (101500, 731962, 160656) AND (CounterID = 101500 OR EventDate != toDate('2014-05-01'))
```

ClickHouse will use the primary key index to trim improper data and the monthly partitioning key to trim partitions that are in improper date ranges.

The queries above show that the index is used even for complex expressions. Reading from the table is organized so that using the index can't be slower than a full scan.

In the example below, the index can't be used.

```sql
SELECT count() FROM table WHERE CounterID = 34 OR URL LIKE '%upyachka%'
```

To check whether ClickHouse can use the index when running a query, use the settings force_index_by_date and force_primary_key.

The key for partitioning by month allows reading only those data blocks which contain dates from the proper range. In this case, the data block may contain data for many dates (up to an entire month). Within a block, data is sorted by primary key, which might not contain the date as the first column. Because of this, using a query with only a date condition that does not specify the primary key prefix will cause more data to be read than for a single date.

#### Use of Index for Partially-monotonic Primary Keys

Consider, for example, the days of the month. They form a monotonic sequence for one month, but not monotonic for more extended periods. This is a partially-monotonic sequence. If a user creates the table with partially-monotonic primary key, ClickHouse creates a sparse index as usual. When a user selects data from this kind of table, ClickHouse analyzes the query conditions. If the user wants to get data between two marks of the index and both these marks fall within one month, ClickHouse can use the index in this particular case because it can calculate the distance between the parameters of a query and index marks.

ClickHouse cannot use an index if the values of the primary key in the query parameter range don't represent a monotonic sequence. In this case, ClickHouse uses the full scan method.

ClickHouse uses this logic not only for days of the month sequences, but for any primary key that represents a partially-monotonic sequence.

#### Data Skipping Indexes

The index declaration is in the columns section of the CREATE query.

```sql
INDEX index_name expr TYPE type(...) GRANULARITY granularity_value
```

For tables from the *MergeTree family, data skipping indices can be specified.

These indices aggregate some information about the specified expression on blocks, which consist of granularity_value granules (the size of the granule is specified using the index_granularity setting in the table engine). Then these aggregates are used in SELECT queries for reducing the amount of data to read from the disk by skipping big blocks of data where the where query cannot be satisfied.

Example:

```sql
CREATE TABLE table_name
(
    u64 UInt64,
    i32 Int32,
    s String,
    ...
    INDEX a (u64 * i32, s) TYPE minmax GRANULARITY 3,
    INDEX b (u64 * length(s)) TYPE set(1000) GRANULARITY 4
) ENGINE = MergeTree()
...
```

Indices from the example can be used by ClickHouse to reduce the amount of data to read from disk in the following queries:

```sql
SELECT count() FROM table WHERE s < 'z'
SELECT count() FROM table WHERE u64 * i32 == 10 AND u64 * length(s) >= 1234
```

##### Available Types of Indices

- `minmax`

  Stores extremes of the specified expression (if the expression is tuple, then it stores extremes for each element of tuple), uses stored info for skipping blocks of data like the primary key.

- `set(max_rows)`

  Stores unique values of the specified expression (no more than max_rows rows, max_rows=0 means "no limits"). Uses the values to check if the WHERE expression is not satisfiable on a block of data.

- `ngrambf_v1(n, size_of_bloom_filter_in_bytes, number_of_hash_functions, random_seed)`

  Stores a Bloom filter that contains all ngrams from a block of data. Works only with strings. Can be used for optimization of equals, like and in expressions.

  - `n` — ngram size,
  - `size_of_bloom_filter_in_bytes` — Bloom filter size in bytes (you can use large values here, for example, 256 or 512, because it can be compressed well).
  - `number_of_hash_functions` — The number of hash functions used in the Bloom filter.
  - `random_seed` — The seed for Bloom filter hash functions.

- `tokenbf_v1(size_of_bloom_filter_in_bytes, number_of_hash_functions, random_seed)`

  The same as `ngrambf_v1`, but stores tokens instead of ngrams. Tokens are sequences separated by non-alphanumeric characters.

- `bloom_filter([false_positive])` — Stores a Bloom filter for the specified columns.

  The optional `false_positive` parameter is the probability of receiving a false positive response from the filter. Possible values: (0, 1). Default value: 0.025.

  Supported data types: `Int*, UInt*, Float*, Enum, Date, DateTime, String, FixedString, Array, LowCardinality, Nullable`.

  The following functions can use it: `equals, notEquals, in, notIn, has`.

```sql
INDEX sample_index (u64 * length(s)) TYPE minmax GRANULARITY 4
INDEX sample_index2 (u64 * length(str), i32 + f64 * 100, date, str) TYPE set(100) GRANULARITY 4
INDEX sample_index3 (lower(str), str) TYPE ngrambf_v1(3, 256, 2, 0) GRANULARITY 4
```

##### Functions Support

Conditions in the `WHERE` clause contains calls of the functions that operate with columns. If the column is a part of an index, ClickHouse tries to use this index when performing the functions. ClickHouse supports different subsets of functions for using indexes.

The `set` index can be used with all functions. Function subsets for other indexes are shown in the table below.

![](./pics/clickhouse/set_index.png)

Functions with a constant argument that is less than ngram size can't be used by `ngrambf_v1` for query optimization.

Bloom filters can have false positive matches, so the `ngrambf_v1`, `tokenbf_v1`, and `bloom_filter` indexes can't be used for optimizing queries where the result of a function is expected to be false, for example:

- Can be optimized:
  - s LIKE '%test%'
  - NOT s NOT LIKE '%test%'
  - s = 1
  - NOT s != 1
  - startsWith(s, 'test')

- Can't be optimized:
  - NOT s LIKE '%test%'
  - s NOT LIKE '%test%'
  - NOT s = 1
  - s != 1
  - NOT startsWith(s, 'test')

#### Concurrent Data Access

For concurrent table access, we use multi-versioning. In other words, when a table is simultaneously read and updated, data is read from a set of parts that is current at the time of the query. There are no lengthy locks. Inserts do not get in the way of read operations.

Reading from a table is automatically parallelized.

#### TTL for Columns and Tables

Determines the lifetime of values.

The `TTL` clause can be set for the whole table and for each individual column. Table-level `TTL` can also specify logic of automatic move of data between disks and volumes.

Expressions must evaluate to `Date` or `DateTime` data type.

Example:

```sql
TTL time_column
TTL time_column + interval
```

To define interval, use time interval operators.

```sql
TTL date_time + INTERVAL 1 MONTH
TTL date_time + INTERVAL 15 HOUR
```

##### Column TTL 

When the values in the column expire, ClickHouse replaces them with the default values for the column data type. If all the column values in the data part expire, ClickHouse deletes this column from the data part in a filesystem.

The `TTL` clause can't be used for key columns.

Examples:

Creating a table with TTL

```sql
CREATE TABLE example_table
(
    d DateTime,
    a Int TTL d + INTERVAL 1 MONTH,
    b Int TTL d + INTERVAL 1 MONTH,
    c String
)
ENGINE = MergeTree
PARTITION BY toYYYYMM(d)
ORDER BY d;
```

Adding TTL to a column of an existing table

```sql
ALTER TABLE example_table
    MODIFY COLUMN
    c String TTL d + INTERVAL 1 DAY;
```

Altering TTL of the column

```sql
ALTER TABLE example_table
    MODIFY COLUMN
    c String TTL d + INTERVAL 1 MONTH;
```

##### Table TTL

Table can have an expression for removal of expired rows, and multiple expressions for automatic move of parts between disks or volumes. When rows in the table expire, ClickHouse deletes all corresponding rows. For parts moving feature, all rows of a part must satisfy the movement expression criteria.

```sql
TTL expr 
    [DELETE|TO DISK 'xxx'|TO VOLUME 'xxx'][, DELETE|TO DISK 'aaa'|TO VOLUME 'bbb'] ...
    [WHERE conditions] 
    [GROUP BY key_expr [SET v1 = aggr_func(v1) [, v2 = aggr_func(v2) ...]] ]   
```

Type of TTL rule may follow each TTL expression. It affects an action which is to be done once the expression is satisfied (reaches current time):

- `DELETE` - delete expired rows (default action);
- `TO DISK 'aaa'` - move part to the disk aaa;
- `TO VOLUME 'bbb'` - move part to the disk bbb;
- `GROUP BY` - aggregate expired rows.

With `WHERE` clause you may specify which of the expired rows to delete or aggregate (it cannot be applied to moves).

`GROUP BY` expression must be a prefix of the table primary key.

If a column is not part of the `GROUP BY` expression and is not set explicitely in the `SET` clause, in result row it contains an occasional value from the grouped rows (as if aggregate function any is applied to it).

Examples

Creating a table with TTL:

```sql
CREATE TABLE example_table
(
    d DateTime,
    a Int
)
ENGINE = MergeTree
PARTITION BY toYYYYMM(d)
ORDER BY d
TTL d + INTERVAL 1 MONTH [DELETE],
    d + INTERVAL 1 WEEK TO VOLUME 'aaa',
    d + INTERVAL 2 WEEK TO DISK 'bbb';
```

Altering TTL of the table:

```sql
ALTER TABLE example_table
    MODIFY TTL d + INTERVAL 1 DAY;
```

Creating a table, where the rows are expired after one month. The expired rows where dates are Mondays are deleted:

```sql
CREATE TABLE table_with_where
(
    d DateTime, 
    a Int
)
ENGINE = MergeTree
PARTITION BY toYYYYMM(d)
ORDER BY d
TTL d + INTERVAL 1 MONTH DELETE WHERE toDayOfWeek(d) = 1;
```

Creating a table, where expired rows are aggregated. In result rows `x` contains the maximum value accross the grouped rows, `y` — the minimum value, and `d` — any occasional value from grouped rows.

```sql
CREATE TABLE table_for_aggregation
(
    d DateTime, 
    k1 Int, 
    k2 Int, 
    x Int, 
    y Int
)
ENGINE = MergeTree
ORDER BY (k1, k2)
TTL d + INTERVAL 1 MONTH GROUP BY k1, k2 SET x = max(x), y = min(y);
```

##### Removing Data

Data with an expired TTL is removed when ClickHouse merges data parts.

When ClickHouse see that data is expired, it performs an off-schedule merge. To control the frequency of such merges, you can set `merge_with_ttl_timeout`. If the value is too low, it will perform many off-schedule merges that may consume a lot of resources.

If you perform the `SELECT` query between merges, you may get expired data. To avoid it, use the `OPTIMIZE` query before `SELECT`.

#### Using Multiple Block Devices for Data Storage

##### Introduction 

`MergeTree` family table engines can store data on multiple block devices. For example, it can be useful when the data of a certain table are implicitly split into "hot" and "cold". The most recent data is regularly requested but requires only a small amount of space. On the contrary, the fat-tailed historical data is requested rarely. If several disks are available, the "hot" data may be located on fast disks (for example, NVMe SSDs or in memory), while the "cold" data - on relatively slow ones (for example, HDD).

Data part is the minimum movable unit for MergeTree-engine tables. The data belonging to one part are stored on one disk. Data parts can be moved between disks in the background (according to user settings) as well as by means of the `ALTER` queries.

##### Terms 
- Disk — Block device mounted to the filesystem.
- Default disk — Disk that stores the path specified in the path server setting.
- Volume — Ordered set of equal disks (similar to JBOD).
- Storage policy — Set of volumes and the rules for moving data between them.

The names given to the described entities can be found in the `system.tables`, `system.storage_policies` and `system.disks`. To apply one of the configured storage policies for a table, use the storage_policy setting of MergeTree-engine family tables.

##### Configuration 

Disks, volumes and storage policies should be declared inside the `<storage_configuration>` tag either in the main file `config.xml` or in a distinct file in the `config.d` directory.

Configuration structure:

```xml
<storage_configuration>
    <disks>
        <disk_name_1> <!-- disk name -->
            <path>/mnt/fast_ssd/clickhouse/</path>
        </disk_name_1>
        <disk_name_2>
            <path>/mnt/hdd1/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk_name_2>
        <disk_name_3>
            <path>/mnt/hdd2/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk_name_3>

        ...
    </disks>

    ...
</storage_configuration>
```

Tags:

- `<disk_name_N>` — Disk name. Names must be different for all disks.
- `path` — path under which a server will store data (data and shadow folders), should be terminated with '/'.
- `keep_free_space_bytes` — the amount of free disk space to be reserved.

The order of the disk definition is not important.

Storage policies configuration markup:

```xml
<storage_configuration>
    ...
    <policies>
        <policy_name_1>
            <volumes>
                <volume_name_1>
                    <disk>disk_name_from_disks_configuration</disk>
                    <max_data_part_size_bytes>1073741824</max_data_part_size_bytes>
                </volume_name_1>
                <volume_name_2>
                    <!-- configuration -->
                </volume_name_2>
                <!-- more volumes -->
            </volumes>
            <move_factor>0.2</move_factor>
        </policy_name_1>
        <policy_name_2>
            <!-- configuration -->
        </policy_name_2>

        <!-- more policies -->
    </policies>
    ...
</storage_configuration>
```

Tags:

- `policy_name_N` — Policy name. Policy names must be unique.
- `volume_name_N` — Volume name. Volume names must be unique.
- `disk` — a disk within a volume.
- `max_data_part_size_bytes` — the maximum size of a part that can be stored on any of the volume's disks.
- `move_factor` — when the amount of available space gets lower than this factor, data automatically start to move on the next volume if any (by default, 0.1).
- `prefer_not_to_merge` — Disables merging of data parts on this volume. When this setting is enabled, merging data on this volume is not allowed. This allows controlling how ClickHouse works with slow disks.

Cofiguration examples:

```xml
<storage_configuration>
    ...
    <policies>
        <hdd_in_order> <!-- policy name -->
            <volumes>
                <single> <!-- volume name -->
                    <disk>disk1</disk>
                    <disk>disk2</disk>
                </single>
            </volumes>
        </hdd_in_order>

        <moving_from_ssd_to_hdd>
            <volumes>
                <hot>
                    <disk>fast_ssd</disk>
                    <max_data_part_size_bytes>1073741824</max_data_part_size_bytes>
                </hot>
                <cold>
                    <disk>disk1</disk>
                </cold>
            </volumes>
            <move_factor>0.2</move_factor>
        </moving_from_ssd_to_hdd>

        <small_jbod_with_external_no_merges>
            <volumes>
                <main>
                    <disk>jbod1</disk>
                </main>
                <external>
                    <disk>external</disk>
                    <prefer_not_to_merge>true</prefer_not_to_merge>
                </external>
            </volumes>
        </small_jbod_with_external_no_merges>
    </policies>
    ...
</storage_configuration>
```

In given example, the `hdd_in_order` policy implements the round-robin approach. Thus this policy defines only one volume (`single`), the data parts are stored on all its disks in circular order. Such policy can be quite useful if there are several similar disks are mounted to the system, but RAID is not configured. Keep in mind that each individual disk drive is not reliable and you might want to compensate it with replication factor of 3 or more.

If there are different kinds of disks available in the system, `moving_from_ssd_to_hdd` policy can be used instead. The volume hot consists of an SSD disk (`fast_ssd`), and the maximum size of a part that can be stored on this volume is 1GB. All the parts with the size larger than 1GB will be stored directly on the cold volume, which contains an HDD disk `disk1`.

Also, once the disk `fast_ssd` gets filled by more than 80%, data will be transferred to the `disk1` by a background process.

The order of volume enumeration within a storage policy is important. Once a volume is overfilled, data are moved to the next one. The order of disk enumeration is important as well because data are stored on them in turns.

When creating a table, one can apply one of the configured storage policies to it:

```sql
CREATE TABLE table_with_non_default_policy (
    EventDate Date,
    OrderID UInt64,
    BannerID UInt64,
    SearchPhrase String
) ENGINE = MergeTree
ORDER BY (OrderID, BannerID)
PARTITION BY toYYYYMM(EventDate)
SETTINGS storage_policy = 'moving_from_ssd_to_hdd'
```

The `default` storage policy implies using only one volume, which consists of only one disk given in `<path>`. Once a table is created, its storage policy cannot be changed.

The number of threads performing background moves of data parts can be changed by `background_move_pool_size` setting.

##### Details

In the case of `MergeTree` tables, data is getting to disk in different ways:

- As a result of an insert (`INSERT` query).
- During background merges and mutations.
- When downloading from another replica.
- As a result of partition freezing `ALTER TABLE … FREEZE PARTITION`.

In all these cases except for mutations and partition freezing, a part is stored on a volume and a disk according to the given storage policy:

1. The first volume (in the order of definition) that has enough disk space for storing a part (`unreserved_space > current_part_size`) and allows for storing parts of a given size (`max_data_part_size_bytes > current_part_size`) is chosen.
2. Within this volume, that disk is chosen that follows the one, which was used for storing the previous chunk of data, and that has free space more than the part size (`unreserved_space - keep_free_space_bytes > current_part_size`).

Under the hood, mutations and partition freezing make use of hard links. Hard links between different disks are not supported, therefore in such cases the resulting parts are stored on the same disks as the initial ones.

In the background, parts are moved between volumes on the basis of the amount of free space (`move_factor` parameter) according to the order the volumes are declared in the configuration file.
Data is never transferred from the last one and into the first one. One may use system tables system.part_log (field `type = MOVE_PART`) and system.parts (fields path and disk) to monitor background moves. Also, the detailed information can be found in server logs.

User can force moving a part or a partition from one volume to another using the query `ALTER TABLE … MOVE PART|PARTITION … TO VOLUME|DISK …`, all the restrictions for background operations are taken into account. The query initiates a move on its own and does not wait for background operations to be completed. User will get an error message if not enough free space is available or if any of the required conditions are not met.

Moving data does not interfere with data replication. Therefore, different storage policies can be specified for the same table on different replicas.

After the completion of background merges and mutations, old parts are removed only after a certain amount of time (`old_parts_lifetime`).
During this time, they are not moved to other volumes or disks. Therefore, until the parts are finally removed, they are still taken into account for evaluation of the occupied disk space.

##### Using S3 for Data Storage

MergeTree family table engines is able to store data to S3 using a disk with type s3.

Configuration markup:

```xml
<storage_configuration>
    ...
    <disks>
        <s3>
            <type>s3</type>
            <endpoint>https://storage.yandexcloud.net/my-bucket/root-path/</endpoint>
            <access_key_id>your_access_key_id</access_key_id>
            <secret_access_key>your_secret_access_key</secret_access_key>
            <server_side_encryption_customer_key_base64>your_base64_encoded_customer_key</server_side_encryption_customer_key_base64>
            <proxy>
                <uri>http://proxy1</uri>
                <uri>http://proxy2</uri>
            </proxy>
            <connect_timeout_ms>10000</connect_timeout_ms>
            <request_timeout_ms>5000</request_timeout_ms>
            <retry_attempts>10</retry_attempts>
            <min_bytes_for_seek>1000</min_bytes_for_seek>
            <metadata_path>/var/lib/clickhouse/disks/s3/</metadata_path>
            <cache_enabled>true</cache_enabled>
            <cache_path>/var/lib/clickhouse/disks/s3/cache/</cache_path>
            <skip_access_check>false</skip_access_check>
        </s3>
    </disks>
    ...
</storage_configuration>
```

Required parameters:

- `endpoint` — S3 endpoint url in path or virtual hosted styles. Endpoint url should contain bucket and root path to store data.
- `access_key_id` — S3 access key id.
- `secret_access_key` — S3 secret access key.

Optional parameters:
- `use_environment_credentials` — Reads AWS credentials from the Environment variables AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY and AWS_SESSION_TOKEN if they exist. Default value is `false`.
- `proxy` — Proxy configuration for S3 endpoint. Each uri element inside proxy block should contain a proxy URL.
- `connect_timeout_ms` — Socket connect timeout in milliseconds. Default value is 10 seconds.
- `request_timeout_ms` — Request timeout in milliseconds. Default value is 5 seconds.
- `retry_attempts` — Number of retry attempts in case of failed request. Default value is 10.
- `min_bytes_for_seek` — Minimal number of bytes to use seek operation instead of sequential read. Default value is 1 Mb.
- `metadata_path` — Path on local FS to store metadata files for S3. Default value is `/var/lib/clickhouse/disks/<disk_name>/`.
- `cache_enabled` — Allows to cache mark and index files on local FS. Default value is true.
- `cache_path` — Path on local FS where to store cached mark and index files. Default value is `/var/lib/clickhouse/disks/<disk_name>/cache/`.
- `skip_access_check` — If true, disk access checks will not be performed on disk start-up. Default value is false.
- `server_side_encryption_customer_key_base64` — If specified, required headers for accessing S3 objects with SSE-C encryption will be set.

S3 disk can be configured as main or cold storage:

```xml
<storage_configuration>
    ...
    <disks>
        <s3>
            <type>s3</type>
            <endpoint>https://storage.yandexcloud.net/my-bucket/root-path/</endpoint>
            <access_key_id>your_access_key_id</access_key_id>
            <secret_access_key>your_secret_access_key</secret_access_key>
        </s3>
    </disks>
    <policies>
        <s3_main>
            <volumes>
                <main>
                    <disk>s3</disk>
                </main>
            </volumes>
        </s3_main>
        <s3_cold>
            <volumes>
                <main>
                    <disk>default</disk>
                </main>
                <external>
                    <disk>s3</disk>
                </external>
            </volumes>
            <move_factor>0.2</move_factor>
        </s3_cold>
    </policies>
    ...
</storage_configuration>
```

In case of cold option a data can be moved to S3 if local disk free size will be smaller than `move_factor * disk_size` or by TTL move rule.

### Data Replication

Replication is only supported for tables in the MergeTree family:

- ReplicatedMergeTree
- ReplicatedSummingMergeTree
- ReplicatedReplacingMergeTree
- ReplicatedAggregatingMergeTree
- ReplicatedCollapsingMergeTree
- ReplicatedVersionedCollapsingMergeTree
- ReplicatedGraphiteMergeTree

Replication works at the level of an individual table, not the entire server. A server can store both replicated and non-replicated tables at the same time.

Replication does not depend on sharding. Each shard has its own independent replication.

Compressed data for `INSERT` and `ALTER` queries is replicated (for more information, see the documentation for `ALTER`).

`CREATE`, `DROP`, `ATTACH`, `DETACH` and `RENAME` queries are executed on a single server and are not replicated:

- The `CREATE TABLE` query creates a new replicatable table on the server where the query is run. If this table already exists on other servers, it adds a new replica.
- The `DROP TABLE` query deletes the replica located on the server where the query is run.
- The `RENAME` query renames the table on one of the replicas. In other words, replicated tables can have different names on different replicas.

*ClickHouse uses Apache ZooKeeper for storing replicas meta information*. Use ZooKeeper version 3.4.5 or newer.

To use replication, set parameters in the zookeeper server configuration section.

Don’t neglect the security setting. ClickHouse supports the digest ACL scheme of the ZooKeeper security subsystem.

Example of setting the addresses of the ZooKeeper cluster:

```xml
<zookeeper>
    <node>
        <host>example1</host>
        <port>2181</port>
    </node>
    <node>
        <host>example2</host>
        <port>2181</port>
    </node>
    <node>
        <host>example3</host>
        <port>2181</port>
    </node>
</zookeeper>
```

ClickHouse also supports to store replicas meta information in the auxiliary ZooKeeper cluster by providing ZooKeeper cluster name and path as engine arguments. In other word, it supports to store the metadata of differnt tables in different ZooKeeper clusters.

Example of setting the addresses of the auxiliary ZooKeeper cluster:

```xml
<auxiliary_zookeepers>
    <zookeeper2>
        <node>
            <host>example_2_1</host>
            <port>2181</port>
        </node>
        <node>
            <host>example_2_2</host>
            <port>2181</port>
        </node>
        <node>
            <host>example_2_3</host>
            <port>2181</port>
        </node>
    </zookeeper2>
    <zookeeper3>
        <node>
            <host>example_3_1</host>
            <port>2181</port>
        </node>
    </zookeeper3>
</auxiliary_zookeepers>
```

To store table datameta in a auxiliary ZooKeeper cluster instead of default ZooKeeper cluster, we can use the SQL to create table with `ReplicatedMergeTree` engine as follow:

```sql
CREATE TABLE table_name ( ... ) ENGINE = ReplicatedMergeTree('zookeeper_name_configured_in_auxiliary_zookeepers:path', 'replica_name') ...
```

You can specify any existing ZooKeeper cluster and the system will use a directory on it for its own data (the directory is specified when creating a replicatable table).

If ZooKeeper isn’t set in the config file, you can’t create replicated tables, and any existing replicated tables will be read-only.

ZooKeeper is not used in `SELECT` queries because replication does not affect the performance of `SELECT` and queries run just as fast as they do for non-replicated tables. When querying distributed replicated tables, ClickHouse behavior is controlled by the settings `max_replica_delay_for_distributed_queries` and `fallback_to_stale_replicas_for_distributed_queries`.

For each `INSERT` query, approximately ten entries are added to ZooKeeper through several transactions. (To be more precise, this is for each inserted block of data; an `INSERT` query contains one block or one block per `max_insert_block_size` = 1048576 rows.) This leads to slightly longer latencies for `INSERT` compared to non-replicated tables. But if you follow the recommendations to insert data in batches of no more than one `INSERT` per second, it doesn’t create any problems. The entire ClickHouse cluster used for coordinating one ZooKeeper cluster has a total of several hundred `INSERT`s per second. The throughput on data inserts (the number of rows per second) is just as high as for non-replicated data.

For very large clusters, you can use different ZooKeeper clusters for different shards. However, this hasn’t proven necessary on the Yandex.Metrica cluster (approximately 300 servers).

Replication is asynchronous and multi-master. `INSERT` queries (as well as ALTER) can be sent to any available server. Data is inserted on the server where the query is run, and then it is copied to the other servers. Because it is asynchronous, recently inserted data appears on the other replicas with some latency. If part of the replicas are not available, the data is written when they become available. If a replica is available, the latency is the amount of time it takes to transfer the block of compressed data over the network. The number of threads performing background tasks for replicated tables can be set by `background_schedule_pool_size` setting.

By default, an `INSERT` query waits for confirmation of writing the data from only one replica. If the data was successfully written to only one replica and the server with this replica ceases to exist, the stored data will be lost. To enable getting confirmation of data writes from multiple replicas, use the `insert_quorum` option.

Each block of data is written atomically. The `INSERT` query is divided into blocks up to `max_insert_block_size = 1048576` rows. In other words, if the `INSERT` query has less than 1048576 rows, it is made atomically.

Data blocks are deduplicated. For multiple writes of the same data block (data blocks of the same size containing the same rows in the same order), the block is only written once. The reason for this is in case of network failures when the client application doesn’t know if the data was written to the DB, so the INSERT query can simply be repeated. It doesn’t matter which replica INSERTs were sent to with identical data. INSERTs are idempotent. Deduplication parameters are controlled by merge_tree server settings.

During replication, only the source data to insert is transferred over the network. Further data transformation (merging) is coordinated and performed on all the replicas in the same way. This minimizes network usage, which means that replication works well when replicas reside in different datacenters. (Note that duplicating data in different datacenters is the main goal of replication.)

You can have any number of replicas of the same data. Yandex.Metrica uses double replication in production. Each server uses RAID-5 or RAID-6, and RAID-10 in some cases. This is a relatively reliable and convenient solution.

The system monitors data synchronicity on replicas and is able to recover after a failure. Failover is automatic (for small differences in data) or semi-automatic (when data differs too much, which may indicate a configuration error).

#### Creating Replicated Tables

The Replicated prefix is added to the table engine name. For example:ReplicatedMergeTree.

**Replicated*MergeTree parameters**

- `zoo_path` — The path to the table in ZooKeeper.
- `replica_name` — The replica name in ZooKeeper.
- `other_parameters` — Parameters of an engine which is used for creating the replicated version, for example, version in ReplacingMergeTree.

**Example**:

```sql
CREATE TABLE table_name
(
    EventDate DateTime,
    CounterID UInt32,
    UserID UInt32,
    ver UInt16
) ENGINE = ReplicatedReplacingMergeTree('/clickhouse/tables/{layer}-{shard}/table_name', '{replica}', ver)
PARTITION BY toYYYYMM(EventDate)
ORDER BY (CounterID, EventDate, intHash32(UserID))
SAMPLE BY intHash32(UserID)
```

As the example shows, these parameters can contain substitutions in curly brackets. The substituted values are taken from the «macros section of the configuration file.

**Example**:

```xml
<macros>
    <layer>05</layer>
    <shard>02</shard>
    <replica>example05-02-1.yandex.ru</replica>
</macros>
```

The path to the table in ZooKeeper should be unique for each replicated table. Tables on different shards should have different paths.
In this case, the path consists of the following parts:

- `/clickhouse/tables/` is the common prefix. We recommend using exactly this one.
- `{layer}-{shard}` is the shard identifier. In this example it consists of two parts, since the Yandex.Metrica cluster uses bi-level sharding. For most tasks, you can leave just the {shard} substitution, which will be expanded to the shard identifier.
- `table_name` is the name of the node for the table in ZooKeeper. It is a good idea to make it the same as the table name. It is defined explicitly, because in contrast to the table name, it doesn’t change after a RENAME query.

HINT: you could add a database name in front of table_name as well. E.g. `db_name.table_name`

The two built-in substitutions `{database}` and `{table}` can be used, they expand into the table name and the database name respectively (unless these macros are defined in the macros section). So the zookeeper path can be specified as `/clickhouse/tables/{layer}-{shard}/{database}/{table}`.

Be careful with table renames when using these built-in substitutions. The path in Zookeeper cannot be changed, and when the table is renamed, the macros will expand into a different path, the table will refer to a path that does not exist in Zookeeper, and will go into read-only mode.

The replica name identifies different replicas of the same table. You can use the server name for this, as in the example. The name only needs to be unique within each shard.

You can define the parameters explicitly instead of using substitutions. This might be convenient for testing and for configuring small clusters. However, you can’t use distributed DDL queries (`ON CLUSTER`) in this case.

When working with large clusters, we recommend using substitutions because they reduce the probability of error.

You can specify default arguments for Replicated table engine in the server configuration file. For instance:

```xml
<default_replica_path>/clickhouse/tables/{shard}/{database}/{table}</default_replica_path>
<default_replica_name>{replica}</default_replica_name>
```

In this case, you can omit arguments when creating tables:

```sql
CREATE TABLE table_name (
    x UInt32
) ENGINE = ReplicatedMergeTree 
ORDER BY x;
```

It is equivalent to:

```sql
CREATE TABLE table_name (
    x UInt32
) ENGINE = ReplicatedMergeTree('/clickhouse/tables/{shard}/{database}/table_name', '{replica}') 
ORDER BY x;
```

Run the `CREATE TABLE` query on each replica. This query creates a new replicated table, or adds a new replica to an existing one.

If you add a new replica after the table already contains some data on other replicas, the data will be copied from the other replicas to the new one after running the query. In other words, the new replica syncs itself with the others.

To delete a replica, run DROP TABLE. However, only one replica is deleted – the one that resides on the server where you run the query.

#### Recovery After Failures

If ZooKeeper is unavailable when a server starts, replicated tables switch to read-only mode. The system periodically attempts to connect to ZooKeeper.

If ZooKeeper is unavailable during an `INSERT`, or an error occurs when interacting with ZooKeeper, an exception is thrown.

After connecting to ZooKeeper, the system checks whether the set of data in the local file system matches the expected set of data (ZooKeeper stores this information). If there are minor inconsistencies, the system resolves them by syncing data with the replicas.

If the system detects broken data parts (with the wrong size of files) or unrecognized parts (parts written to the file system but not recorded in ZooKeeper), it moves them to the detached subdirectory (they are not deleted). Any missing parts are copied from the replicas.

Note that ClickHouse does not perform any destructive actions such as automatically deleting a large amount of data.

When the server starts (or establishes a new session with ZooKeeper), it only checks the quantity and sizes of all files. If the file sizes match but bytes have been changed somewhere in the middle, this is not detected immediately, but only when attempting to read the data for a SELECT query. The query throws an exception about a non-matching checksum or size of a compressed block. In this case, data parts are added to the verification queue and copied from the replicas if necessary.

If the local set of data differs too much from the expected one, a safety mechanism is triggered. The server enters this in the log and refuses to launch. The reason for this is that this case may indicate a configuration error, such as if a replica on a shard was accidentally configured like a replica on a different shard. However, the thresholds for this mechanism are set fairly low, and this situation might occur during normal failure recovery. In this case, data is restored semi-automatically - by “pushing a button”.

To start recovery, create the node `/path_to_table/replica_name/flags/force_restore_data` in ZooKeeper with any content, or run the command to restore all replicated tables:

```shell
sudo -u clickhouse touch /var/lib/clickhouse/flags/force_restore_data
```

Then restart the server. On start, the server deletes these flags and starts recovery.

#### Recovery After Complete Data Loss

If all data and metadata disappeared from one of the servers, follow these steps for recovery:

1. Install ClickHouse on the server. Define substitutions correctly in the config file that contains the shard identifier and replicas, if you use them.
2. If you had unreplicated tables that must be manually duplicated on the servers, copy their data from a replica (in the directory `/var/lib/clickhouse/data/db_name/table_name/`).
3. Copy table definitions located in `/var/lib/clickhouse/metadata/` from a replica. If a shard or replica identifier is defined explicitly in the table definitions, correct it so that it corresponds to this replica. (Alternatively, start the server and make all the `ATTACH TABLE` queries that should have been in the .sql files in `/var/lib/clickhouse/metadata/`.)
4. To start recovery, create the ZooKeeper node `/path_to_table/replica_name/flags/force_restore_data` with any content, or run the command to restore all replicated tables: `sudo -u clickhouse touch /var/lib/clickhouse/flags/force_restore_data`
Then start the server (restart, if it is already running). Data will be downloaded from replicas.

An alternative recovery option is to delete information about the lost replica from ZooKeeper (`/path_to_table/replica_name`), then create the replica again as described in “Creating replicated tables”.

There is no restriction on network bandwidth during recovery. Keep this in mind if you are restoring many replicas at once.

#### Converting from MergeTree to ReplicatedMergeTree

We use the term MergeTree to refer to all table engines in the MergeTree family, the same as for `ReplicatedMergeTree`.

If you had a `MergeTree` table that was manually replicated, you can convert it to a replicated table. You might need to do this if you have already collected a large amount of data in a `MergeTree` table and now you want to enable replication.

If the data differs on various replicas, first sync it, or delete this data on all the replicas except one.

1. Rename the existing `MergeTree` table, then create a `ReplicatedMergeTree` table with the old name.
2. Move the data from the old table to the detached subdirectory inside the directory with the new table data (`/var/lib/clickhouse/data/db_name/table_name/`).
3. Then run `ALTER TABLE ATTACH PARTITION` on one of the replicas to add these data parts to the working set.

#### Converting from ReplicatedMergeTree to MergeTree 

Create a MergeTree table with a different name. Move all the data from the directory with the `ReplicatedMergeTree` table data to the new table’s data directory. Then delete the `ReplicatedMergeTree` table and restart the server.

If you want to get rid of a `ReplicatedMergeTree` table without launching the server:

- Delete the corresponding .sql file in the metadata directory (`/var/lib/clickhouse/metadata/`).
- Delete the corresponding path in ZooKeeper (`/path_to_table/replica_name`).

After this, you can launch the server, create a `MergeTree` table, move the data to its directory, and then restart the server.

#### Recovery When Metadata in the Zookeeper Cluster Is Lost or Damaged 

If the data in ZooKeeper was lost or damaged, you can save data by moving it to an unreplicated table as described above.

### Custom Partitioning Key

Partitioning is available for the MergeTree family tables (including replicated tables). Materialized views based on MergeTree tables support partitioning, as well.

A partition is a logical combination of records in a table by a specified criterion. You can set a partition by an arbitrary criterion, such as by month, by day, or by event type. Each partition is stored separately to simplify manipulations of this data. When accessing the data, ClickHouse uses the smallest subset of partitions possible.

The partition is specified in the `PARTITION BY` expr clause when creating a table. The partition key can be any expression from the table columns. For example, to specify partitioning by month, use the expression `toYYYYMM(date_column)`:

```sql
CREATE TABLE visits
(
    VisitDate Date,
    Hour UInt8,
    ClientID UUID
)
ENGINE = MergeTree()
PARTITION BY toYYYYMM(VisitDate)
ORDER BY Hour;
```

The partition key can also be a tuple of expressions (similar to the primary key). For example:

```sql
ENGINE = ReplicatedCollapsingMergeTree('/clickhouse/tables/name', 'replica1', Sign)
PARTITION BY (toMonday(StartDate), EventType)
ORDER BY (CounterID, StartDate, intHash32(UserID));
```

In this example, we set partitioning by the event types that occurred during the current week.

When inserting new data to a table, this data is stored as a separate part (chunk) sorted by the primary key. In 10-15 minutes after inserting, the parts of the same partition are merged into the entire part.

A merge only works for data parts that have the same value for the partitioning expression. This means you shouldn’t make overly granular partitions (more than about a thousand partitions). Otherwise, the `SELECT` query performs poorly because of an unreasonably large number of files in the file system and open file descriptors.

Use the `system.parts` table to view the table parts and partitions. For example, let’s assume that we have a visits table with partitioning by month. Let’s perform the `SELECT` query for the `system.parts` table:

```sql
SELECT
    partition,
    name,
    active
FROM system.parts
WHERE table = 'visits'

┌─partition─┬─name───────────┬─active─┐
│ 201901    │ 201901_1_3_1   │      0 │
│ 201901    │ 201901_1_9_2   │      1 │
│ 201901    │ 201901_8_8_0   │      0 │
│ 201901    │ 201901_9_9_0   │      0 │
│ 201902    │ 201902_4_6_1   │      1 │
│ 201902    │ 201902_10_10_0 │      1 │
│ 201902    │ 201902_11_11_0 │      1 │
└───────────┴────────────────┴────────┘
```

The `partition` column contains the names of the partitions. There are two partitions in this example: `201901` and `201902`. You can use this column value to specify the partition name in `ALTER` … `PARTITION` queries.

The name column contains the names of the partition data parts. You can use this column to specify the name of the part in the `ALTER ATTACH PART` query.

Let’s break down the name of the first part: `201901_1_3_1`:

- 201901 is the partition name.
- 1 is the minimum number of the data block.
- 3 is the maximum number of the data block.
- 1 is the chunk level (the depth of the merge tree it is formed from).

The parts of old-type tables have the name: `20190117_20190123_2_2_0 (minimum date - maximum date - minimum block number - maximum block number - level)`.

The `active` column shows the status of the part. 1 is active; 0 is inactive. The inactive parts are, for example, source parts remaining after merging to a larger part. The corrupted data parts are also indicated as inactive.

As you can see in the example, there are several separated parts of the same partition (for example, `201901_1_3_1` and `201901_1_9_2`). This means that these parts are not merged yet. ClickHouse merges the inserted parts of data periodically, approximately 15 minutes after inserting. In addition, you can perform a non-scheduled merge using the `OPTIMIZE` query. Example:

```sql
OPTIMIZE TABLE visits PARTITION 201902;

┌─partition─┬─name───────────┬─active─┐
│ 201901    │ 201901_1_3_1   │      0 │
│ 201901    │ 201901_1_9_2   │      1 │
│ 201901    │ 201901_8_8_0   │      0 │
│ 201901    │ 201901_9_9_0   │      0 │
│ 201902    │ 201902_4_6_1   │      0 │
│ 201902    │ 201902_4_11_2  │      1 │
│ 201902    │ 201902_10_10_0 │      0 │
│ 201902    │ 201902_11_11_0 │      0 │
└───────────┴────────────────┴────────┘
```

Inactive parts will be deleted approximately 10 minutes after merging.

Another way to view a set of parts and partitions is to go into the directory of the table: `/var/lib/clickhouse/data/<database>/<table>/`. For example:

```shell
/var/lib/clickhouse/data/default/visits$ ls -l
total 40
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb  1 16:48 201901_1_3_1
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb  5 16:17 201901_1_9_2
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb  5 15:52 201901_8_8_0
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb  5 15:52 201901_9_9_0
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb  5 16:17 201902_10_10_0
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb  5 16:17 201902_11_11_0
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb  5 16:19 201902_4_11_2
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb  5 12:09 201902_4_6_1
drwxr-xr-x 2 clickhouse clickhouse 4096 Feb  1 16:48 detached
```

The folders ‘201901_1_1_0’, ‘201901_1_7_1’ and so on are the directories of the parts. Each part relates to a corresponding partition and contains data just for a certain month (the table in this example has partitioning by month).

The detached directory contains parts that were detached from the table using the `DETACH` query. The corrupted parts are also moved to this directory, instead of being deleted. The server does not use the parts from the detached directory. You can add, delete, or modify the data in this directory at any time – the server will not know about this until you run the `ATTACH` query.

Note that on the operating server, you cannot manually change the set of parts or their data on the file system, since the server will not know about it. For non-replicated tables, you can do this when the server is stopped, but it isn’t recommended. For replicated tables, the set of parts cannot be changed in any case.

ClickHouse allows you to perform operations with the partitions: delete them, copy from one table to another, or create a backup. See the list of all operations in the section Manipulations With Partitions and Parts.

### ReplacingMergeTree

The engine differs from MergeTree in that it removes duplicate entries with the same sorting key value (`ORDER BY` table section, not PRIMARY KEY).

Data deduplication occurs only during a merge. Merging occurs in the background at an unknown time, so you can’t plan for it. Some of the data may remain unprocessed. Although you can run an unscheduled merge using the `OPTIMIZE` query, don’t count on using it, because the `OPTIMIZE` query will read and write a large amount of data.

Thus, ReplacingMergeTree is suitable for clearing out duplicate data in the background in order to save space, *but it doesn’t guarantee the absence of duplicates*.

#### Creating a Table

```sql
CREATE TABLE [IF NOT EXISTS] [db.]table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT|MATERIALIZED|ALIAS expr1],
    name2 [type2] [DEFAULT|MATERIALIZED|ALIAS expr2],
    ...
) ENGINE = ReplacingMergeTree([ver])
[PARTITION BY expr]
[ORDER BY expr]
[PRIMARY KEY expr]
[SAMPLE BY expr]
[SETTINGS name=value, ...]
```

For a description of request parameters, see statement description.

Uniqueness of rows is determined by the `ORDER BY` table section, not `PRIMARY KEY`.

**ReplacingMergeTree Parameters**

- `ver` — column with version. `Type UInt*`, `Date` or `DateTime`. Optional parameter.
  
  When merging, `ReplacingMergeTree` from all the rows with the same sorting key leaves only one:

  - The last in the selection, if ver not set. A selection is a set of rows in a set of parts participating in the merge. The most recently created part (the last insert) will be the last one in the selection. Thus, after deduplication, the very last row from the most recent insert will remain for each unique sorting key.
  - With the maximum version, if ver specified.

**Query clauses**

When creating a `ReplacingMergeTree` table the same clauses are required, as when creating a `MergeTree` table.

### SummingMergeTree

The engine inherits from MergeTree. The difference is that when merging data parts for `SummingMergeTree` tables ClickHouse replaces all the rows with the same primary key (or more accurately, with the same sorting key) with one row which contains summarized values for the columns with the numeric data type. If the sorting key is composed in a way that a single key value corresponds to large number of rows, this significantly reduces storage volume and speeds up data selection.

We recommend to use the engine together with MergeTree. Store complete data in MergeTree table, and use `SummingMergeTree` for aggregated data storing, for example, when preparing reports. Such an approach will prevent you from losing valuable data due to an incorrectly composed primary key.

#### Creating a Table

```sql
CREATE TABLE [IF NOT EXISTS] [db.]table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT|MATERIALIZED|ALIAS expr1],
    name2 [type2] [DEFAULT|MATERIALIZED|ALIAS expr2],
    ...
) ENGINE = SummingMergeTree([columns])
[PARTITION BY expr]
[ORDER BY expr]
[SAMPLE BY expr]
[SETTINGS name=value, ...]
```

For a description of request parameters, see request description.

**Parameters of SummingMergeTree**

- `columns` - a tuple with the names of columns where values will be summarized. Optional parameter.
  The columns must be of a numeric type and must not be in the primary key.

  If columns not specified, ClickHouse summarizes the values in all columns with a numeric data type that are not in the primary key.

**Query clauses**

When creating a `SummingMergeTree` table the same clauses are required, as when creating a MergeTree table.

#### Usage Example 

Consider the following table:

```sql
CREATE TABLE summtt
(
    key UInt32,
    value UInt32
)
ENGINE = SummingMergeTree()
ORDER BY key
```

Insert data to it:

```sql
INSERT INTO summtt Values(1,1),(1,2),(2,1)
```

ClickHouse may sum all the rows not completely (see below), so we use an aggregate function sum and `GROUP BY` clause in the query.

```sql
SELECT key, sum(value) FROM summtt GROUP BY key

┌─key─┬─sum(value)─┐
│   2 │          1 │
│   1 │          3 │
└─────┴────────────┘
```

#### Data Processing 

When data are inserted into a table, they are saved as-is. ClickHouse merges the inserted parts of data periodically and this is when rows with the same primary key are summed and replaced with one for each resulting part of data.

ClickHouse can merge the data parts so that different resulting parts of data cat consist rows with the same primary key, i.e. the summation will be incomplete. Therefore `(SELECT)` an aggregate function `sum()` and `GROUP BY` clause should be used in a query as described in the example above.

##### Common Rules for Summation

- The values in the columns with the numeric data type are summarized. The set of columns is defined by the parameter `columns`.
- If the values were 0 in all of the columns for summation, the row is deleted.
- If column is not in the primary key and is not summarized, an arbitrary value is selected from the existing ones.
- The values are not summarized for columns in the primary key.

##### The Summation in the Aggregatefunction Columns 

For columns of AggregateFunction type ClickHouse behaves as `AggregatingMergeTree` engine aggregating according to the function.

##### Nested Structures 

Table can have nested data structures that are processed in a special way.

If the name of a nested table ends with Map and it contains at least two columns that meet the following criteria:

- the first column is numeric `(*Int*, Date, DateTime)` or a string `(String, FixedString)`, let’s call it key,
- the other columns are arithmetic `(*Int*, Float32/64)`, let’s call it `(values...)`,

then this nested table is interpreted as a mapping of `key => (values...)`, and when merging its rows, the elements of two data sets are merged by key with a summation of the corresponding `(values...)`.

Examples:

```
[(1, 100)] + [(2, 150)] -> [(1, 100), (2, 150)]
[(1, 100)] + [(1, 150)] -> [(1, 250)]
[(1, 100)] + [(1, 150), (2, 150)] -> [(1, 250), (2, 150)]
[(1, 100), (2, 150)] + [(1, -100)] -> [(2, 150)]
```

When requesting data, use the sumMap(key, value) function for aggregation of `Map`.

For nested data structure, you do not need to specify its columns in the tuple of columns for summation.

### Aggregatingmergetree

The engine inherits from MergeTree, altering the logic for data parts merging. ClickHouse replaces all rows with the same primary key (or more accurately, with the same sorting key) with a single row (within a one data part) that stores a combination of states of aggregate functions.

You can use `AggregatingMergeTree` tables for incremental data aggregation, including for aggregated materialized views.

The engine processes all columns with the following types:

- AggregateFunction
- SimpleAggregateFunction

It is appropriate to use `AggregatingMergeTree` if it reduces the number of rows by orders.

#### Creating a Table 

```sql
CREATE TABLE [IF NOT EXISTS] [db.]table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT|MATERIALIZED|ALIAS expr1],
    name2 [type2] [DEFAULT|MATERIALIZED|ALIAS expr2],
    ...
) ENGINE = AggregatingMergeTree()
[PARTITION BY expr]
[ORDER BY expr]
[SAMPLE BY expr]
[TTL expr]
[SETTINGS name=value, ...]
```

For a description of request parameters, see request description.

**Query clauses**

When creating a AggregatingMergeTree table the same clauses are required, as when creating a MergeTree table.

#### SELECT and INSERT 

To insert data, use INSERT SELECT query with aggregate -State- functions.
When selecting data from `AggregatingMergeTree` table, use `GROUP BY` clause and the same aggregate functions as when inserting data, but using `-Merge` suffix.

In the results of `SELECT` query, the values of `AggregateFunction` type have implementation-specific binary representation for all of the ClickHouse output formats. If dump data into, for example, TabSeparated format with `SELECT` query then this dump can be loaded back using `INSERT` query.

#### Example of an Aggregated Materialized View 

`AggregatingMergeTree` materialized view that watches the test.visits table:

```sql
CREATE MATERIALIZED VIEW test.basic
ENGINE = AggregatingMergeTree() PARTITION BY toYYYYMM(StartDate) ORDER BY (CounterID, StartDate)
AS SELECT
    CounterID,
    StartDate,
    sumState(Sign)    AS Visits,
    uniqState(UserID) AS Users
FROM test.visits
GROUP BY CounterID, StartDate;
```

Inserting data into the test.visits table.

```sql
INSERT INTO test.visits ...
```

The data are inserted in both the table and view `test.basic` that will perform the aggregation.

To get the aggregated data, we need to execute a query such as `SELECT ... GROUP BY ...` from the view `test.basic`:

```sql
SELECT
    StartDate,
    sumMerge(Visits) AS Visits,
    uniqMerge(Users) AS Users
FROM test.basic
GROUP BY StartDate
ORDER BY StartDate;
```

### CollapsingMergeTree

The engine inherits from MergeTree and adds the logic of rows collapsing to data parts merge algorithm.

`CollapsingMergeTree` asynchronously deletes (collapses) pairs of rows if all of the fields in a sorting key (ORDER BY) are equivalent excepting the particular field `Sign` which can have 1 and -1 values. Rows without a pair are kept. For more details see the Collapsing section of the document.

The engine may significantly reduce the volume of storage and increase the efficiency of `SELECT` query as a consequence.

#### Creating a Table

```sql
CREATE TABLE [IF NOT EXISTS] [db.]table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT|MATERIALIZED|ALIAS expr1],
    name2 [type2] [DEFAULT|MATERIALIZED|ALIAS expr2],
    ...
) ENGINE = CollapsingMergeTree(sign)
[PARTITION BY expr]
[ORDER BY expr]
[SAMPLE BY expr]
[SETTINGS name=value, ...]
```

For a description of query parameters, see query description.

**CollapsingMergeTree Parameters**

- sign — Name of the column with the type of row: 1 is a “state” row, -1 is a “cancel” row.
  
  Column data type — Int8.

**Query clauses**

When creating a CollapsingMergeTree table, the same query clauses are required, as when creating a MergeTree table.

#### Collapsing

##### Data

Consider the situation where you need to save continually changing data for some object. It sounds logical to have one row for an object and update it at any change, but update operation is expensive and slow for DBMS because it requires rewriting of the data in the storage. If you need to write data quickly, update not acceptable, but you can write the changes of an object sequentially as follows.

Use the particular column `Sign`. If `Sign` = 1 it means that the row is a state of an object, let’s call it “state” row. If `Sign` = -1 it means the cancellation of the state of an object with the same attributes, let’s call it “cancel” row.

For example, we want to calculate how much pages users checked at some site and how long they were there. At some moment we write the following row with the state of user activity:

```
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │         5 │      146 │    1 │
└─────────────────────┴───────────┴──────────┴──────┘
```

At some moment later we register the change of user activity and write it with the following two rows.

```
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │         5 │      146 │   -1 │
│ 4324182021466249494 │         6 │      185 │    1 │
└─────────────────────┴───────────┴──────────┴──────┘
```

The first row cancels the previous state of the object (user). It should copy the sorting key fields of the cancelled state excepting Sign.

The second row contains the current state.

As we need only the last state of user activity, the rows

```
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │         5 │      146 │    1 │
│ 4324182021466249494 │         5 │      146 │   -1 │
└─────────────────────┴───────────┴──────────┴──────┘
```

can be deleted collapsing the invalid (old) state of an object. CollapsingMergeTree does this while merging of the data parts.

Why we need 2 rows for each change read in the Algorithm paragraph.

**Peculiar properties of such approach**

1. The program that writes the data should remember the state of an object to be able to cancel it. “Cancel” string should contain copies of the sorting key fields of the “state” string and the opposite Sign. It increases the initial size of storage but allows to write the data quickly.
2. Long growing arrays in columns reduce the efficiency of the engine due to load for writing. The more straightforward data, the higher the efficiency.
3. The `SELECT` results depend strongly on the consistency of object changes history. Be accurate when preparing data for inserting. You can get unpredictable results in inconsistent data, for example, negative values for non-negative metrics such as session depth.

##### Algorithm

When ClickHouse merges data parts, each group of consecutive rows with the same sorting key (ORDER BY) is reduced to not more than two rows, one with Sign = 1 (“state” row) and another with Sign = -1 (“cancel” row). In other words, entries collapse.

For each resulting data part ClickHouse saves:

1. The first “cancel” and the last “state” rows, if the number of “state” and “cancel” rows matches and the last row is a “state” row.
2. The last “state” row, if there are more “state” rows than “cancel” rows.
3. The first “cancel” row, if there are more “cancel” rows than “state” rows.
4. None of the rows, in all other cases.

Also when there are at least 2 more “state” rows than “cancel” rows, or at least 2 more “cancel” rows then “state” rows, the merge continues, but ClickHouse treats this situation as a logical error and records it in the server log. This error can occur if the same data were inserted more than once.

Thus, collapsing should not change the results of calculating statistics.
Changes gradually collapsed so that in the end only the last state of almost every object left.

The `Sign` is required because the merging algorithm doesn’t guarantee that all of the rows with the same sorting key will be in the same resulting data part and even on the same physical server. ClickHouse process `SELECT` queries with multiple threads, and it can not predict the order of rows in the result. The aggregation is required if there is a need to get completely “collapsed” data from `CollapsingMergeTree` table.

To finalize collapsing, write a query with `GROUP BY` clause and aggregate functions that account for the sign. For example, to calculate quantity, use `sum(Sign)` instead of `count()`. To calculate the sum of something, use `sum(Sign * x)` instead of `sum(x)`, and so on, and also add `HAVING sum(Sign) > 0`.

The aggregates `count`, `sum` and `avg` could be calculated this way. The aggregate uniq could be calculated if an object has at least one state not collapsed. The aggregates min and max could not be calculated because CollapsingMergeTree does not save the values history of the collapsed states.

If you need to extract data without aggregation (for example, to check whether rows are present whose newest values match certain conditions), you can use the `FINAL` modifier for the FROM clause. This approach is significantly less efficient.

#### Example of Use

Example data:

```
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │         5 │      146 │    1 │
│ 4324182021466249494 │         5 │      146 │   -1 │
│ 4324182021466249494 │         6 │      185 │    1 │
└─────────────────────┴───────────┴──────────┴──────┘
```

Creation of the table:

```sql
CREATE TABLE UAct
(
    UserID UInt64,
    PageViews UInt8,
    Duration UInt8,
    Sign Int8
)
ENGINE = CollapsingMergeTree(Sign)
ORDER BY UserID
Insertion of the data:

￼INSERT INTO UAct VALUES (4324182021466249494, 5, 146, 1)
￼INSERT INTO UAct VALUES (4324182021466249494, 5, 146, -1),(4324182021466249494, 6, 185, 1)
```

We use two INSERT queries to create two different data parts. If we insert the data with one query ClickHouse creates one data part and will not perform any merge ever.

Getting the data:

```sql
SELECT * FROM UAct
￼┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │         5 │      146 │   -1 │
│ 4324182021466249494 │         6 │      185 │    1 │
└─────────────────────┴───────────┴──────────┴──────┘
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │         5 │      146 │    1 │
└─────────────────────┴───────────┴──────────┴──────┘
```

What do we see and where is collapsing?

With two `INSERT` queries, we created 2 data parts. The `SELECT` query was performed in 2 threads, and we got a random order of rows. Collapsing not occurred because there was no merge of the data parts yet. ClickHouse merges data part in an unknown moment which we can not predict.

Thus we need aggregation:

```sql
SELECT
    UserID,
    sum(PageViews * Sign) AS PageViews,
    sum(Duration * Sign) AS Duration
FROM UAct
GROUP BY UserID
HAVING sum(Sign) > 0

┌──────────────UserID─┬─PageViews─┬─Duration─┐
│ 4324182021466249494 │         6 │      185 │
└─────────────────────┴───────────┴──────────┘
```

If we do not need aggregation and want to force collapsing, we can use FINAL modifier for FROM clause.

```sql
SELECT * FROM UAct FINAL

┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │         6 │      185 │    1 │
└─────────────────────┴───────────┴──────────┴──────┘
```

This way of selecting the data is very inefficient. Don’t use it for big tables.

#### Example of Another Approach

Example data:

```
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │         5 │      146 │    1 │
│ 4324182021466249494 │        -5 │     -146 │   -1 │
│ 4324182021466249494 │         6 │      185 │    1 │
└─────────────────────┴───────────┴──────────┴──────┘
```

The idea is that merges take into account only key fields. And in the “Cancel” line we can specify negative values that equalize the previous version of the row when summing without using the Sign column. For this approach, it is necessary to change the data type PageViews,Duration to store negative values of UInt8 -> Int16.

```sql
CREATE TABLE UAct
(
    UserID UInt64,
    PageViews Int16,
    Duration Int16,
    Sign Int8
)
ENGINE = CollapsingMergeTree(Sign)
ORDER BY UserID
```

Let’s test the approach:

```sql
insert into UAct values(4324182021466249494,  5,  146,  1);
insert into UAct values(4324182021466249494, -5, -146, -1);
insert into UAct values(4324182021466249494,  6,  185,  1);

select * from UAct final; // avoid using final in production (just for a test or small tables)

┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │         6 │      185 │    1 │
└─────────────────────┴───────────┴──────────┴──────┘

SELECT
    UserID,
    sum(PageViews) AS PageViews,
    sum(Duration) AS Duration
FROM UAct
GROUP BY UserID
┌──────────────UserID─┬─PageViews─┬─Duration─┐
│ 4324182021466249494 │         6 │      185 │
└─────────────────────┴───────────┴──────────┘

select count() FROM UAct
┌─count()─┐
│       3 │
└─────────┘
optimize table UAct final;

select * FROM UAct
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┐
│ 4324182021466249494 │         6 │      185 │    1 │
└─────────────────────┴───────────┴──────────┴──────┘
```

### VersionedCollapsingMergeTree

This engine:

- Allows quick writing of object states that are continually changing.
- Deletes old object states in the background. This significantly reduces the volume of storage.

See the section Collapsing for details.

The engine inherits from MergeTree and adds the logic for collapsing rows to the algorithm for merging data parts. VersionedCollapsingMergeTree serves the same purpose as CollapsingMergeTree but uses a different collapsing algorithm that allows inserting the data in any order with multiple threads. In particular, the Version column helps to collapse the rows properly even if they are inserted in the wrong order. In contrast, CollapsingMergeTree allows only strictly consecutive insertion.

#### Creating a Table

```sql
CREATE TABLE [IF NOT EXISTS] [db.]table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT|MATERIALIZED|ALIAS expr1],
    name2 [type2] [DEFAULT|MATERIALIZED|ALIAS expr2],
    ...
) ENGINE = VersionedCollapsingMergeTree(sign, version)
[PARTITION BY expr]
[ORDER BY expr]
[SAMPLE BY expr]
[SETTINGS name=value, ...]
```

For a description of query parameters, see the query description.

**Engine Parameters**

```sql
VersionedCollapsingMergeTree(sign, version)
```

- `sign` — Name of the column with the type of row: 1 is a “state” row, -1 is a “cancel” row.

  The column data type should be Int8.

- `version` — Name of the column with the version of the object state.

  The column data type should be UInt*.

**Query Clauses**

When creating a `VersionedCollapsingMergeTree` table, the same clauses are required as when creating a MergeTree table.

#### Collapsing 

##### Data 

Consider a situation where you need to save continually changing data for some object. It is reasonable to have one row for an object and update the row whenever there are changes. However, the update operation is expensive and slow for a DBMS because it requires rewriting the data in the storage. Update is not acceptable if you need to write data quickly, but you can write the changes to an object sequentially as follows.

Use the Sign column when writing the row. If `Sign = 1` it means that the row is a state of an object (let’s call it the “state” row). If `Sign = -1` it indicates the cancellation of the state of an object with the same attributes (let’s call it the “cancel” row). Also use the Version column, which should identify each state of an object with a separate number.

For example, we want to calculate how many pages users visited on some site and how long they were there. At some point in time we write the following row with the state of user activity:

```
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │         5 │      146 │    1 │       1 |
└─────────────────────┴───────────┴──────────┴──────┴─────────┘
```

At some point later we register the change of user activity and write it with the following two rows.

```
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │         5 │      146 │   -1 │       1 |
│ 4324182021466249494 │         6 │      185 │    1 │       2 |
└─────────────────────┴───────────┴──────────┴──────┴─────────┘
```

The first row cancels the previous state of the object (user). It should copy all of the fields of the canceled state except Sign.

The second row contains the current state.

Because we need only the last state of user activity, the rows

```
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │         5 │      146 │    1 │       1 |
│ 4324182021466249494 │         5 │      146 │   -1 │       1 |
└─────────────────────┴───────────┴──────────┴──────┴─────────┘
```

can be deleted, collapsing the invalid (old) state of the object. `VersionedCollapsingMergeTree` does this while merging the data parts.

To find out why we need two rows for each change, see Algorithm.

**Notes on Usage**

1. The program that writes the data should remember the state of an object to be able to cancel it. “Cancel” string should contain copies of the primary key fields and the version of the “state” string and the opposite Sign. It increases the initial size of storage but allows to write the data quickly.
2. Long growing arrays in columns reduce the efficiency of the engine due to the load for writing. The more straightforward the data, the better the efficiency.
3. SELECT results depend strongly on the consistency of the history of object changes. Be accurate when preparing data for inserting. You can get unpredictable results with inconsistent data, such as negative values for non-negative metrics like session depth.


##### Algorithm 

When ClickHouse merges data parts, it deletes each pair of rows that have the same primary key and version and different Sign. The order of rows does not matter.

When ClickHouse inserts data, it orders rows by the primary key. If the Version column is not in the primary key, ClickHouse adds it to the primary key implicitly as the last field and uses it for ordering.

#### Selecting Data 

ClickHouse doesn’t guarantee that all of the rows with the same primary key will be in the same resulting data part or even on the same physical server. This is true both for writing the data and for subsequent merging of the data parts. In addition, ClickHouse processes `SELECT` queries with multiple threads, and it cannot predict the order of rows in the result. This means that aggregation is required if there is a need to get completely “collapsed” data from a `VersionedCollapsingMergeTree` table.

To finalize collapsing, write a query with a `GROUP BY` clause and aggregate functions that account for the sign. For example, to calculate quantity, use `sum(Sign)` instead of `count()`. To calculate the sum of something, use `sum(Sign * x)` instead of `sum(x)`, and add `HAVING sum(Sign) > 0`.

The aggregates count, sum and avg can be calculated this way. The aggregate uniq can be calculated if an object has at least one non-collapsed state. The aggregates min and max can’t be calculated because VersionedCollapsingMergeTree does not save the history of values of collapsed states.

If you need to extract the data with “collapsing” but without aggregation (for example, to check whether rows are present whose newest values match certain conditions), you can use the FINAL modifier for the FROM clause. This approach is inefficient and should not be used with large tables.

##### Example of Use 

Example data:

```
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │         5 │      146 │    1 │       1 |
│ 4324182021466249494 │         5 │      146 │   -1 │       1 |
│ 4324182021466249494 │         6 │      185 │    1 │       2 |
└─────────────────────┴───────────┴──────────┴──────┴─────────┘
```

Creating the table:

```sql
CREATE TABLE UAct
(
    UserID UInt64,
    PageViews UInt8,
    Duration UInt8,
    Sign Int8,
    Version UInt8
)
ENGINE = VersionedCollapsingMergeTree(Sign, Version)
ORDER BY UserID
```

Inserting the data:

```sql
￼INSERT INTO UAct VALUES (4324182021466249494, 5, 146, 1, 1)
￼INSERT INTO UAct VALUES (4324182021466249494, 5, 146, -1, 1),(4324182021466249494, 6, 185, 1, 2)
```

We use two `INSERT` queries to create two different data parts. If we insert the data with a single query, ClickHouse creates one data part and will never perform any merge.

Getting the data:

```sql
SELECT * FROM UAct
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │         5 │      146 │    1 │       1 │
└─────────────────────┴───────────┴──────────┴──────┴─────────┘
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │         5 │      146 │   -1 │       1 │
│ 4324182021466249494 │         6 │      185 │    1 │       2 │
└─────────────────────┴───────────┴──────────┴──────┴─────────┘
```

What do we see here and where are the collapsed parts?
We created two data parts using two INSERT queries. The SELECT query was performed in two threads, and the result is a random order of rows.
Collapsing did not occur because the data parts have not been merged yet. ClickHouse merges data parts at an unknown point in time which we cannot predict.

This is why we need aggregation:

```sql
SELECT
    UserID,
    sum(PageViews * Sign) AS PageViews,
    sum(Duration * Sign) AS Duration,
    Version
FROM UAct
GROUP BY UserID, Version
HAVING sum(Sign) > 0

┌──────────────UserID─┬─PageViews─┬─Duration─┬─Version─┐
│ 4324182021466249494 │         6 │      185 │       2 │
└─────────────────────┴───────────┴──────────┴─────────┘
```
If we don’t need aggregation and want to force collapsing, we can use the FINAL modifier for the FROM clause.

```sql
SELECT * FROM UAct FINAL
┌──────────────UserID─┬─PageViews─┬─Duration─┬─Sign─┬─Version─┐
│ 4324182021466249494 │         6 │      185 │    1 │       2 │
└─────────────────────┴───────────┴──────────┴──────┴─────────┘
```

This is a very inefficient way to select data. Don’t use it for large tables.

### GraphiteMergeTree

This engine is designed for thinning and aggregating/averaging (rollup) Graphite data. It may be helpful to developers who want to use ClickHouse as a data store for Graphite.

You can use any ClickHouse table engine to store the Graphite data if you don’t need rollup, but if you need a rollup use GraphiteMergeTree. The engine reduces the volume of storage and increases the efficiency of queries from Graphite.

The engine inherits properties from MergeTree.

#### Creating a Table 

```sql
CREATE TABLE [IF NOT EXISTS] [db.]table_name [ON CLUSTER cluster]
(
    Path String,
    Time DateTime,
    Value <Numeric_type>,
    Version <Numeric_type>
    ...
) ENGINE = GraphiteMergeTree(config_section)
[PARTITION BY expr]
[ORDER BY expr]
[SAMPLE BY expr]
[SETTINGS name=value, ...]
```

See a detailed description of the CREATE TABLE query.

A table for the Graphite data should have the following columns for the following data:

- Metric name (Graphite sensor). Data type: String.
- Time of measuring the metric. Data type: DateTime.
- Value of the metric. Data type: any numeric.
- Version of the metric. Data type: any numeric.
  ClickHouse saves the rows with the highest version or the last written if versions are the same. Other rows are deleted during the merge of data parts.

The names of these columns should be set in the rollup configuration.

**GraphiteMergeTree parameters**

- `config_section` — Name of the section in the configuration file, where are the rules of rollup set.

**Query clauses**

When creating a `GraphiteMergeTree` table, the same clauses are required, as when creating a MergeTree table.

#### Rollup Configuration 

The settings for rollup are defined by the `graphite_rollup` parameter in the server configuration. The name of the parameter could be any. You can create several configurations and use them for different tables.

Rollup configuration structure:

```
￼ required-columns
  patterns
```

##### Required Columns 

- `path_column_name` — The name of the column storing the metric name (Graphite sensor). Default value: Path.
- `time_column_name` — The name of the column storing the time of measuring the metric. Default value: Time.
- `value_column_name` — The name of the column storing the value of the metric at the time set in `time_column_name`. Default value: Value.
- `version_column_name` — The name of the column storing the version of the metric. Default value: Timestamp.

##### Patterns 

Structure of the patterns section:

```shell
￼pattern
    regexp
    function
pattern
    regexp
    age + precision
    ...
pattern
    regexp
    function
    age + precision
    ...
pattern
    ...
default
    function
    age + precision
    ...
```

**Attention**

Patterns must be strictly ordered:

1. Patterns without `function` or `retention`.
2. Patterns with both `function` and `retention`.
3. Pattern `default`.

When processing a row, ClickHouse checks the rules in the `pattern` sections. Each of `pattern` (including `default`) sections can contain function parameter for aggregation, retention parameters or both. If the metric name matches the regexp, the rules from the `pattern` section (or sections) are applied; otherwise, the rules from the default section are used.

Fields for `pattern` and `default` sections:

- `regexp–` A pattern for the metric name.
- `age` – The minimum age of the data in seconds.
- `precision–` How precisely to define the age of the data in seconds. Should be a divisor for 86400 (seconds in a day).
- `function` – The name of the aggregating function to apply to data whose age falls within the range [age, age + precision].


##### Configuration Example

```xml
￼<graphite_rollup>
    <version_column_name>Version</version_column_name>
    <pattern>
        <regexp>click_cost</regexp>
        <function>any</function>
        <retention>
            <age>0</age>
            <precision>5</precision>
        </retention>
        <retention>
            <age>86400</age>
            <precision>60</precision>
        </retention>
    </pattern>
    <default>
        <function>max</function>
        <retention>
            <age>0</age>
            <precision>60</precision>
        </retention>
        <retention>
            <age>3600</age>
            <precision>300</precision>
        </retention>
        <retention>
            <age>86400</age>
            <precision>3600</precision>
        </retention>
    </default>
</graphite_rollup>
```

### Log Engine Family

These engines were developed for scenarios when you need to quickly write many small tables (up to about 1 million rows) and read them later as a whole.

Engines of the family:

- StripeLog
- Log
- TinyLog

#### Common Properties 

Engines:

- Store data on a disk.
- Append data to the end of file when writing.
- Support locks for concurrent data access.
- During `INSERT` queries, the table is locked, and other queries for reading and writing data both wait for the table to unlock. If there are no data writing queries, any number of data reading queries can be performed concurrently.
- Do not support mutations.
- Do not support indexes.
  This means that `SELECT` queries for ranges of data are not efficient.
- Do not write data atomically.

  You can get a table with corrupted data if something breaks the write operation, for example, abnormal server shutdown.

#### Differences 

The `TinyLog` engine is the simplest in the family and provides the poorest functionality and lowest efficiency. The `TinyLog` engine doesn’t support parallel data reading by several threads in a single query. It reads data slower than other engines in the family that support parallel reading from a single query and it uses almost as many file descriptors as the Log engine because it stores each column in a separate file. Use it only in simple scenarios.

The Log and `StripeLog` engines support parallel data reading. When reading data, ClickHouse uses multiple threads. Each thread processes a separate data block. The Log engine uses a separate file for each column of the table. `StripeLog` stores all the data in one file. As a result, the `StripeLog` engine uses fewer file descriptors, but the Log engine provides higher efficiency when reading data.

### Stripelog 

This engine belongs to the family of log engines. See the common properties of log engines and their differences in the Log Engine Family article.

Use this engine in scenarios when you need to write many tables with a small amount of data (less than 1 million rows).

#### Creating a Table 

```sql
CREATE TABLE [IF NOT EXISTS] [db.]table_name [ON CLUSTER cluster]
(
    column1_name [type1] [DEFAULT|MATERIALIZED|ALIAS expr1],
    column2_name [type2] [DEFAULT|MATERIALIZED|ALIAS expr2],
    ...
) ENGINE = StripeLog
```

See the detailed description of the `CREATE TABLE` query.

#### Writing the Data 

The `StripeLog` engine stores all the columns in one file. For each `INSERT` query, ClickHouse appends the data block to the end of a table file, writing columns one by one.

For each table ClickHouse writes the files:
- `data.bin` — Data file.
- `index.mrk` — File with marks. Marks contain offsets for each column of each data block inserted.
- The StripeLog engine does not support the `ALTER UPDATE` and `ALTER DELETE` operations.

#### Reading the Data 

The file with marks allows ClickHouse to parallelize the reading of data. This means that a `SELECT` query returns rows in an unpredictable order. Use the ORDER BY clause to sort rows.

#### Example of Use 

Creating a table:

```sql
CREATE TABLE stripe_log_table
(
    timestamp DateTime,
    message_type String,
    message String
)
ENGINE = StripeLog
```

Inserting data:

```sql
INSERT INTO stripe_log_table VALUES (now(),'REGULAR','The first regular message')
INSERT INTO stripe_log_table VALUES (now(),'REGULAR','The second regular message'),(now(),'WARNING','The first warning message')
```

We used two INSERT queries to create two data blocks inside the data.bin file.

ClickHouse uses multiple threads when selecting data. Each thread reads a separate data block and returns resulting rows independently as it finishes. As a result, the order of blocks of rows in the output does not match the order of the same blocks in the input in most cases. For example:

```sql
SELECT * FROM stripe_log_table
┌───────────timestamp─┬─message_type─┬─message────────────────────┐
│ 2019-01-18 14:27:32 │ REGULAR      │ The second regular message │
│ 2019-01-18 14:34:53 │ WARNING      │ The first warning message  │
└─────────────────────┴──────────────┴────────────────────────────┘
┌───────────timestamp─┬─message_type─┬─message───────────────────┐
│ 2019-01-18 14:23:43 │ REGULAR      │ The first regular message │
└─────────────────────┴──────────────┴───────────────────────────┘
```

Sorting the results (ascending order by default):

```sql
SELECT * FROM stripe_log_table ORDER BY timestamp
┌───────────timestamp─┬─message_type─┬─message────────────────────┐
│ 2019-01-18 14:23:43 │ REGULAR      │ The first regular message  │
│ 2019-01-18 14:27:32 │ REGULAR      │ The second regular message │
│ 2019-01-18 14:34:53 │ WARNING      │ The first warning message  │
└─────────────────────┴──────────────┴────────────────────────────┘
```

### Log 

Engine belongs to the family of log engines. See the common properties of log engines and their differences in the Log Engine Family article.

Log differs from TinyLog in that a small file of “marks” resides with the column files. These marks are written on every data block and contain offsets that indicate where to start reading the file in order to skip the specified number of rows. This makes it possible to read table data in multiple threads.

For concurrent data access, the read operations can be performed simultaneously, while write operations block reads and each other.

The Log engine does not support indexes. Similarly, if writing to a table failed, the table is broken, and reading from it returns an error. The Log engine is appropriate for temporary data, write-once tables, and for testing or demonstration purposes.

### TinyLog 

The engine belongs to the log engine family. See Log Engine Family for common properties of log engines and their differences.

This table engine is typically used with the write-once method: write data one time, then read it as many times as necessary. For example, you can use TinyLog-type tables for intermediary data that is processed in small batches. Note that storing data in a large number of small tables is inefficient.

Queries are executed in a single stream. In other words, this engine is intended for relatively small tables (up to about 1,000,000 rows). It makes sense to use this table engine if you have many small tables, since it’s simpler than the Log engine (fewer files need to be opened).


### Distributed Table Engine

Tables with Distributed engine do not store any data by their own, but allow distributed query processing on multiple servers. Reading is automatically parallelized. During a read, the table indexes on remote servers are used, if there are any.

The Distributed engine accepts parameters:
- the cluster name in the server’s config file
- the name of a remote database
- the name of a remote table
- (optionally) sharding key
- (optionally) policy name, it will be used to store temporary files for async send

  See also:
  - insert_distributed_sync setting
  - MergeTree for the examples

Also it accept the following settings:

- fsync_after_insert - do the fsync for the file data after asynchronous insert to Distributed. Guarantees that the OS flushed the whole inserted data to a file on the initiator node disk.
- fsync_directories - do the fsync for directories. Guarantees that the OS refreshed directory metadata after operations related to asynchronous inserts on Distributed table (after insert, after sending the data to shard, etc).
- bytes_to_throw_insert - if more than this number of compressed bytes will be pending for async INSERT, an exception will be thrown. 0 - do not throw. Default 0.
- bytes_to_delay_insert - if more than this number of compressed bytes will be pending for async INSERT, the query will be delayed. 0 - do not delay. Default 0.
- max_delay_to_insert - max delay of inserting data into Distributed table in seconds, if there are a lot of pending bytes for async send. Default 60.

#### Note

##### Durability settings (fsync_...):

- Affect only asynchronous INSERTs (i.e. insert_distributed_sync=false) when data first stored on the initiator node disk and later asynchronously send to shards.
- May significantly decrease the inserts' performance
- Affect writing the data stored inside Distributed table folder into the node which accepted your insert. If you need to have guarantees of writing data to underlying MergeTree tables - see durability settings (...fsync...) in system.merge_tree_settings


For Insert limit settings (..._insert) see also:

- insert_distributed_sync setting
- prefer_localhost_replica setting
- bytes_to_throw_insert handled before bytes_to_delay_insert, so you should not set it to the value less then bytes_to_delay_insert


Example:

```sql
Distributed(logs, default, hits[, sharding_key[, policy_name]])
SETTINGS
    fsync_after_insert=0,
    fsync_directories=0;
```

Data will be read from all servers in the logs cluster, from the default.hits table located on every server in the cluster. Data is not only read but is partially processed on the remote servers (to the extent that this is possible). For example, for a query with GROUP BY, data will be aggregated on remote servers, and the intermediate states of aggregate functions will be sent to the requestor server. Then data will be further aggregated.

Instead of the database name, you can use a constant expression that returns a string. For example: currentDatabase().

logs – The cluster name in the server’s config file.

Clusters are set like this:

```xml
<remote_servers>
    <logs>
        <!-- Inter-server per-cluster secret for Distributed queries
             default: no secret (no authentication will be performed)

             If set, then Distributed queries will be validated on shards, so at least:
             - such cluster should exist on the shard,
             - such cluster should have the same secret.

             And also (and which is more important), the initial_user will
             be used as current user for the query.
        -->
        <!-- <secret></secret> -->
        <shard>
            <!-- Optional. Shard weight when writing data. Default: 1. -->
            <weight>1</weight>
            <!-- Optional. Whether to write data to just one of the replicas. Default: false (write data to all replicas). -->
            <internal_replication>false</internal_replication>
            <replica>
                <!-- Optional. Priority of the replica for load balancing (see also load_balancing setting). Default: 1 (less value has more priority). -->
                <priority>1</priority>
                <host>example01-01-1</host>
                <port>9000</port>
            </replica>
            <replica>
                <host>example01-01-2</host>
                <port>9000</port>
            </replica>
        </shard>
        <shard>
            <weight>2</weight>
            <internal_replication>false</internal_replication>
            <replica>
                <host>example01-02-1</host>
                <port>9000</port>
            </replica>
            <replica>
                <host>example01-02-2</host>
                <secure>1</secure>
                <port>9440</port>
            </replica>
        </shard>
    </logs>
</remote_servers>
```

Here a cluster is defined with the name logs that consists of two shards, each of which contains two replicas. Shards refer to the servers that contain different parts of the data (in order to read all the data, you must access all the shards). Replicas are duplicating servers (in order to read all the data, you can access the data on any one of the replicas).

Cluster names must not contain dots.

The parameters host, port, and optionally user, password, secure, compression are specified for each server:
- `host` – The address of the remote server. You can use either the domain or the IPv4 or IPv6 address. If you specify the domain, the server makes a DNS request when it starts, and the result is stored as long as the server is running. If the DNS request fails, the server doesn’t start. If you change the DNS record, restart the server.
- `port` – The TCP port for messenger activity (`tcp_port` in the config, usually set to 9000). Do not confuse it with `http_port`.
- `user` – Name of the user for connecting to a remote server. Default value: default. This user must have access to connect to the specified server. Access is configured in the users.xml file. For more information, see the section Access rights.
- `password` – The password for connecting to a remote server (not masked). Default value: empty string.
- `secure` - Use ssl for connection, usually you also should define port = 9440. Server should listen on `<tcp_port_secure>9440</tcp_port_secure>` and have correct certificates.
- `compression` - Use data compression. Default value: true.

When specifying replicas, one of the available replicas will be selected for each of the shards when reading. You can configure the algorithm for load balancing (the preference for which replica to access) – see the `load_balancing` setting.

If the connection with the server is not established, there will be an attempt to connect with a short timeout. If the connection failed, the next replica will be selected, and so on for all the replicas. If the connection attempt failed for all the replicas, the attempt will be repeated the same way, several times.

This works in favour of resiliency, but does not provide complete fault tolerance: a remote server might accept the connection, but might not work, or work poorly.

You can specify just one of the shards (in this case, query processing should be called remote, rather than distributed) or up to any number of shards. In each shard, you can specify from one to any number of replicas. You can specify a different number of replicas for each shard.

You can specify as many clusters as you wish in the configuration.

To view your clusters, use the system.clusters table.

The Distributed engine allows working with a cluster like a local server. However, the cluster is inextensible: you must write its configuration in the server config file (even better, for all the cluster’s servers).

The Distributed engine requires writing clusters to the config file. Clusters from the config file are updated on the fly, without restarting the server. If you need to send a query to an unknown set of shards and replicas each time, you don’t need to create a Distributed table – use the remote table function instead. See the section Table functions.

There are two methods for writing data to a cluster:

First, you can define which servers to write which data to and perform the write directly on each shard. In other words, perform `INSERT` in the tables that the distributed table “looks at”. This is the most flexible solution as you can use any sharding scheme, which could be non-trivial due to the requirements of the subject area. This is also the most optimal solution since data can be written to different shards completely independently.

Second, you can perform `INSERT` in a Distributed table. In this case, the table will distribute the inserted data across the servers itself. In order to write to a Distributed table, it must have a sharding key set (the last parameter). In addition, if there is only one shard, the write operation works without specifying the sharding key, since it doesn’t mean anything in this case.

Each shard can have a weight defined in the config file. By default, the weight is equal to one. Data is distributed across shards in the amount proportional to the shard weight. For example, if there are two shards and the first has a weight of 9 while the second has a weight of 10, the first will be sent 9 / 19 parts of the rows, and the second will be sent 10 / 19.

Each shard can have the `internal_replication` parameter defined in the config file.

If this parameter is set to true, the write operation selects the first healthy replica and writes data to it. Use this alternative if the Distributed table “looks at” replicated tables. In other words, if the table where data will be written is going to replicate them itself.

If it is set to false (the default), data is written to all replicas. In essence, this means that the Distributed table replicates data itself. This is worse than using replicated tables, because the consistency of replicas is not checked, and over time they will contain slightly different data.

To select the shard that a row of data is sent to, the sharding expression is analyzed, and its remainder is taken from dividing it by the total weight of the shards. The row is sent to the shard that corresponds to the half-interval of the remainders from prev_weight to `prev_weights + weight`, where `prev_weights` is the total weight of the shards with the smallest number, and weight is the weight of this shard. For example, if there are two shards, and the first has a weight of 9 while the second has a weight of 10, the row will be sent to the first shard for the remainders from the range [0, 9), and to the second for the remainders from the range [9, 19).

The sharding expression can be any expression from constants and table columns that returns an integer. For example, you can use the expression `rand()` for random distribution of data, or UserID for distribution by the remainder from dividing the user’s `ID` (then the data of a single user will reside on a single shard, which simplifies running `IN` and `JOIN` by users). If one of the columns is not distributed evenly enough, you can wrap it in a hash function: `intHash64(UserID)`.

A simple reminder from the division is a limited solution for sharding and isn’t always appropriate. It works for medium and large volumes of data (dozens of servers), but not for very large volumes of data (hundreds of servers or more). In the latter case, use the sharding scheme required by the subject area, rather than using entries in Distributed tables.

`SELECT` queries are sent to all the shards and work regardless of how data is distributed across the shards (they can be distributed completely randomly). When you add a new shard, you don’t have to transfer the old data to it. You can write new data with a heavier weight – the data will be distributed slightly unevenly, but queries will work correctly and efficiently.

You should be concerned about the sharding scheme in the following cases:

Queries are used that require joining data (`IN` or `JOIN`) by a specific key. If data is sharded by this key, you can use local `IN` or `JOIN` instead of GLOBAL IN or GLOBAL JOIN, which is much more efficient.

A large number of servers is used (hundreds or more) with a large number of small queries (queries of individual clients - websites, advertisers, or partners). In order for the small queries to not affect the entire cluster, it makes sense to locate data for a single client on a single shard. Alternatively, as we’ve done in Yandex.Metrica, you can set up bi-level sharding: divide the entire cluster into “layers”, where a layer may consist of multiple shards. Data for a single client is located on a single layer, but shards can be added to a layer as necessary, and data is randomly distributed within them. Distributed tables are created for each layer, and a single shared distributed table is created for global queries.

Data is written asynchronously. When inserted in the table, the data block is just written to the local file system. The data is sent to the remote servers in the background as soon as possible. The period for sending data is managed by the `distributed_directory_monitor_sleep_time_ms` and `distributed_directory_monitor_max_sleep_time_ms` settings. The Distributed engine sends each file with inserted data separately, but you can enable batch sending of files with the `distributed_directory_monitor_batch_inserts` setting. This setting improves cluster performance by better utilizing local server and network resources. You should check whether data is sent successfully by checking the list of files (data waiting to be sent) in the table directory: `/var/lib/clickhouse/data/database/table/`. The number of threads performing background tasks can be set by `background_distributed_schedule_pool_size` setting.

If the server ceased to exist or had a rough restart (for example, after a device failure) after an `INSERT` to a Distributed table, the inserted data might be lost. If a damaged data part is detected in the table directory, it is transferred to the broken subdirectory and no longer used.

When the max_parallel_replicas option is enabled, query processing is parallelized across all replicas within a single shard. For more information, see the section max_parallel_replicas.

#### Virtual Columns 
- `_shard_num` — Contains the `shard_num` (from `system.clusters`). Type: UInt32.
  
**Note**

Since `remote/cluster` table functions internally create temporary instance of the same Distributed engine, `_shard_num` is available there too.

See Also
- Virtual columns
- background_distributed_schedule_pool_size

### Dictionary Table Engine 

The Dictionary engine displays the dictionary data as a ClickHouse table.

Example 
As an example, consider a dictionary of products with the following configuration:

```xml
<dictionaries>
    <dictionary>
        <name>products</name>
        <source>
            <odbc>
                <table>products</table>
                <connection_string>DSN=some-db-server</connection_string>
            </odbc>
        </source>
        <lifetime>
            <min>300</min>
            <max>360</max>
        </lifetime>
        <layout>
            <flat/>
        </layout>
        <structure>
            <id>
                <name>product_id</name>
            </id>
            <attribute>
                <name>title</name>
                <type>String</type>
                <null_value></null_value>
            </attribute>
        </structure>
    </dictionary>
</dictionaries>
```

Query the dictionary data:

```sql
SELECT
    name,
    type,
    key,
    attribute.names,
    attribute.types,
    bytes_allocated,
    element_count,
    source
FROM system.dictionaries
WHERE name = 'products'
```

┌─name─────┬─type─┬─key────┬─attribute.names─┬─attribute.types─┬─bytes_allocated─┬─element_count─┬─source──────────┐
│ products │ Flat │ UInt64 │ ['title']       │ ['String']      │        23065376 │        175032 │ ODBC: .products │
└──────────┴──────┴────────┴─────────────────┴─────────────────┴─────────────────┴───────────────┴─────────────────┘

You can use the `dictGet*` function to get the dictionary data in this format.

This view isn’t helpful when you need to get raw data, or when performing a JOIN operation. For these cases, you can use the Dictionary engine, which displays the dictionary data in a table.

Syntax:

```sql
CREATE TABLE %table_name% (%fields%) engine = Dictionary(%dictionary_name%)`
```

Usage example:

```sql
create table products (product_id UInt64, title String) Engine = Dictionary(products);
￼  Ok
```

Take a look at what’s in the table.

```sql
select * from products limit 1;
┌────product_id─┬─title───────────┐
│        152689 │ Some item       │
└───────────────┴─────────────────┘
```

### Merge Table Engine 

The Merge engine (not to be confused with `MergeTree`) does not store data itself, but allows reading from any number of other tables simultaneously.

Reading is automatically parallelized. Writing to a table is not supported. When reading, the indexes of tables that are actually being read are used, if they exist.

The Merge engine accepts parameters: the database name and a regular expression for tables.

**Examples** 

Example 1:

```sql
Merge(hits, '^WatchLog')
```

Data will be read from the tables in the hits database that have names that match the regular expression ‘^WatchLog’.

Instead of the database name, you can use a constant expression that returns a string. For example, `currentDatabase()`.

Regular expressions — re2 (supports a subset of `PCRE`), case-sensitive.

See the notes about escaping symbols in regular expressions in the “match” section.

When selecting tables to read, the Merge table itself will not be selected, even if it matches the regex. This is to avoid loops.
It is possible to create two Merge tables that will endlessly try to read each others’ data, but this is not a good idea.

The typical way to use the Merge engine is for working with a large number of TinyLog tables as if with a single table.

Example 2:

Let’s say you have a old table (`WatchLog_old`) and decided to change partitioning without moving data to a new table (`WatchLog_new`) and you need to see data from both tables.

```sql
CREATE TABLE WatchLog_old(date Date, UserId Int64, EventType String, Cnt UInt64)
ENGINE=MergeTree(date, (UserId, EventType), 8192);
INSERT INTO WatchLog_old VALUES ('2018-01-01', 1, 'hit', 3);

CREATE TABLE WatchLog_new(date Date, UserId Int64, EventType String, Cnt UInt64)
ENGINE=MergeTree PARTITION BY date ORDER BY (UserId, EventType) SETTINGS index_granularity=8192;
INSERT INTO WatchLog_new VALUES ('2018-01-02', 2, 'hit', 3);

CREATE TABLE WatchLog as WatchLog_old ENGINE=Merge(currentDatabase(), '^WatchLog');

SELECT *
FROM WatchLog
┌───────date─┬─UserId─┬─EventType─┬─Cnt─┐
│ 2018-01-01 │      1 │ hit       │   3 │
└────────────┴────────┴───────────┴─────┘
┌───────date─┬─UserId─┬─EventType─┬─Cnt─┐
│ 2018-01-02 │      2 │ hit       │   3 │
└────────────┴────────┴───────────┴─────┘
```

#### Virtual Columns 

- `_table` — Contains the name of the table from which data was read. Type: String.

  You can set the constant conditions on `_table` in the WHERE/PREWHERE clause (for example, `WHERE _table='xyz'`). In this case the read operation is performed only for that tables where the condition on `_table` is satisfied, so the `_table` column acts as an index.

### File Table Engine 

The File table engine keeps the data in a file in one of the supported file formats (TabSeparated, Native, etc.).

Usage scenarios:

- Data export from ClickHouse to file.
- Convert data from one format to another.
- Updating data in ClickHouse via editing a file on a disk.


#### Usage in ClickHouse Server 

```sql
File(Format)
```

The Format parameter specifies one of the available file formats. To perform `SELECT` queries, the format must be supported for input, and to perform `INSERT` queries – for output. The available formats are listed in the Formats section.

ClickHouse does not allow to specify filesystem path forFile. It will use folder defined by path setting in server configuration.

When creating table using File(Format) it creates empty subdirectory in that folder. When data is written to that table, it’s put into data.Format file in that subdirectory.

You may manually create this subfolder and file in server filesystem and then ATTACH it to table information with matching name, so you can query data from that file.

**Warning**

Be careful with this functionality, because ClickHouse does not keep track of external changes to such files. The result of simultaneous writes via ClickHouse and outside of ClickHouse is undefined.

Example 

1. Set up the `file_engine_table` table:

```sql
CREATE TABLE file_engine_table (name String, value UInt32) ENGINE=File(TabSeparated)
```

By default ClickHouse will create folder `/var/lib/clickhouse/data/default/file_engine_table`.

2. Manually create `/var/lib/clickhouse/data/default/file_engine_table/dataTabSeparated` containing:

```shell
$ cat data.TabSeparated
one 1
two 2
```

3. Query the data:

```sql
SELECT * FROM file_engine_table
┌─name─┬─value─┐
│ one  │     1 │
│ two  │     2 │
└──────┴───────┘
```

#### Usage in ClickHouse-local 

In clickhouse-local File engine accepts file path in addition to Format. Default input/output streams can be specified using numeric or human-readable names like 0 or stdin, 1 or stdout. It is possible to read and write compressed files based on an additional engine parameter or file extension (gz, br or xz).

Example:

```shell
$ echo -e "1,2\n3,4" | clickhouse-local -q "CREATE TABLE table (a Int64, b  Int64) ENGINE = File(CSV, stdin); SELECT a, b FROM table; DROP TABLE table"
```

#### Details of Implementation 

- Multiple `SELECT` queries can be performed concurrently, but `INSERT` queries will wait each other.
- Supported creating new file by `INSERT` query.
- If file exists, `INSERT` would append new values in it.

### Null Table Engine 
When writing to a Null table, data is ignored. When reading from a Null table, the response is empty.

**Hint**

However, you can create a materialized view on a Null table. So the data written to the table will end up affecting the view, but original raw data will still be discarded.

### Set Table Engine

A data set that is always in RAM. It is intended for use on the right side of the IN operator (see the section “IN operators”).

You can use `INSERT` to insert data in the table. New elements will be added to the data set, while duplicates will be ignored.
But you can’t perform `SELECT` from the table. The only way to retrieve data is by using it in the right half of the IN operator.

Data is always located in RAM. For `INSERT`, the blocks of inserted data are also written to the directory of tables on the disk. When starting the server, this data is loaded to RAM. In other words, after restarting, the data remains in place.

For a rough server restart, the block of data on the disk might be lost or damaged. In the latter case, you may need to manually delete the file with damaged data.

#### Limitations and Settings 

When creating a table, the following settings are applied:
- persistent

### Join Table Engine 

Optional prepared data structure for usage in JOIN operations.

**Note**

This is not an article about the `JOIN` clause itself.

#### Creating a Table 

```sql
CREATE TABLE [IF NOT EXISTS] [db.]table_name [ON CLUSTER cluster]
(
    name1 [type1] [DEFAULT|MATERIALIZED|ALIAS expr1] [TTL expr1],
    name2 [type2] [DEFAULT|MATERIALIZED|ALIAS expr2] [TTL expr2],
) ENGINE = Join(join_strictness, join_type, k1[, k2, ...])
```

See the detailed description of the `CREATE TABLE` query.

**Engine Parameters**

- `join_strictness` – `JOIN` strictness.
- `join_type` – `JOIN` type.
- `k1[, k2, ...]` – Key columns from the USING clause that the `JOIN` operation is made with.

Enter `join_strictness` and `join_type` parameters without quotes, for example, `Join(ANY, LEFT, col1)`. They must match the `JOIN` operation that the table will be used for. If the parameters don’t match, ClickHouse doesn’t throw an exception and may return incorrect data.

#### Table Usage 

**Example**

Creating the left-side table:

```sql
CREATE TABLE id_val(`id` UInt32, `val` UInt32) ENGINE = TinyLog
INSERT INTO id_val VALUES (1,11)(2,12)(3,13)
```

Creating the right-side Join table:

```sql
CREATE TABLE id_val_join(`id` UInt32, `val` UInt8) ENGINE = Join(ANY, LEFT, id)
INSERT INTO id_val_join VALUES (1,21)(1,22)(3,23)
```

Joining the tables:

```sql
SELECT * FROM id_val ANY LEFT JOIN id_val_join USING (id) SETTINGS join_use_nulls = 1
┌─id─┬─val─┬─id_val_join.val─┐
│  1 │  11 │              21 │
│  2 │  12 │           ᴺᵁᴸᴸ │
│  3 │  13 │              23 │
└────┴─────┴─────────────────┘
```

As an alternative, you can retrieve data from the Join table, specifying the join key value:

```sql
SELECT joinGet('id_val_join', 'val', toUInt32(1))
┌─joinGet('id_val_join', 'val', toUInt32(1))─┐
│                                         21 │
└────────────────────────────────────────────┘
```

#### Selecting and Inserting Data 

You can use `INSERT` queries to add data to the Join-engine tables. If the table was created with the `ANY` strictness, data for duplicate keys are ignored. With the `ALL` strictness, all rows are added.

You cannot perform a `SELECT` query directly from the table. Instead, use one of the following methods:

Place the table to the right side in a `JOIN` clause.
Call the joinGet function, which lets you extract data from the table the same way as from a dictionary.

#### Limitations and Settings 

When creating a table, the following settings are applied:

- `join_use_nulls`
- `max_rows_in_join`
- `max_bytes_in_join`
- `join_overflow_mode`
- `join_any_take_last_row`
- `persistent`

The Join-engine tables can’t be used in `GLOBAL JOIN` operations.

The Join-engine allows use `join_use_nulls` setting in the `CREATE TABLE` statement. And `SELECT` query allows use `join_use_nulls` too. If you have different `join_use_nulls` settings, you can get an error joining table. It depends on kind of JOIN. When you use joinGet function, you have to use the same `join_use_nulls` setting in CRATE TABLE and `SELECT` statements.

#### Data Storage 

Join table data is always located in the RAM. When inserting rows into a table, ClickHouse writes data blocks to the directory on the disk so that they can be restored when the server restarts.

If the server restarts incorrectly, the data block on the disk might get lost or damaged. In this case, you may need to manually delete the file with damaged data.

### URL Table Engine 
Queries data to/from a remote HTTP/HTTPS server. This engine is similar to the File engine.

Syntax: URL(URL, Format)

#### Usage 

The format must be one that ClickHouse can use in `SELECT` queries and, if necessary, in `INSERT`s. For the full list of supported formats, see Formats.

The URL must conform to the structure of a Uniform Resource Locator. The specified URL must point to a server that uses HTTP or HTTPS. This does not require any additional headers for getting a response from the server.

`INSERT` and `SELECT` queries are transformed to POST and GET requests, respectively. For processing POST requests, the remote server must support Chunked transfer encoding.

You can limit the maximum number of HTTP GET redirect hops using the `max_http_get_redirects` setting.

#### Example

1. Create a url_engine_table table on the server :

```sql
CREATE TABLE url_engine_table (word String, value UInt64)
ENGINE=URL('http://127.0.0.1:12345/', CSV)
```

2. Create a basic HTTP server using the standard Python 3 tools and
start it:

```python
from http.server import BaseHTTPRequestHandler, HTTPServer

class CSVHTTPServer(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/csv')
        self.end_headers()

        self.wfile.write(bytes('Hello,1\nWorld,2\n', "utf-8"))

if __name__ == "__main__":
    server_address = ('127.0.0.1', 12345)
    HTTPServer(server_address, CSVHTTPServer).serve_forever()
```

```shell
$ python3 server.py
```

3. Request data:

```sql
SELECT * FROM url_engine_table
┌─word──┬─value─┐
│ Hello │     1 │
│ World │     2 │
└───────┴───────┘
```

#### Details of Implementation 
- Reads and writes can be parallel

### View Table Engine 

Used for implementing views (for more information, see the `CREATE VIEW` query). It does not store data, but only stores the specified `SELECT` query. When reading from a table, it runs this query (and deletes all unnecessary columns from the query).

### MaterializedView Table Engine 

Used for implementing materialized views (for more information, see `CREATE VIEW`). For storing data, it uses a different engine that was specified when creating the view. When reading from a table, it just uses that engine.

### Memory Table Engine 

The Memory engine stores data in RAM, in uncompressed form. Data is stored in exactly the same form as it is received when read. In other words, reading from this table is completely free. Concurrent data access is synchronized. Locks are short: read and write operations don’t block each other. Indexes are not supported. Reading is parallelized.

Maximal productivity (over 10 GB/sec) is reached on simple queries, because there is no reading from the disk, decompressing, or deserializing data. (We should note that in many cases, the productivity of the MergeTree engine is almost as high.) When restarting a server, data disappears from the table and the table becomes empty.

Normally, using this table engine is not justified. However, it can be used for tests, and for tasks where maximum speed is required on a relatively small number of rows (up to approximately 100,000,000).

The Memory engine is used by the system for temporary tables with external query data (see the section “External data for processing a query”), and for implementing `GLOBAL IN` (see the section “IN operators”).

### Buffer Table Engine 

Buffers the data to write in RAM, periodically flushing it to another table. During the read operation, data is read from the buffer and the other table simultaneously.

```sql
Buffer(database, table, num_layers, min_time, max_time, min_rows, max_rows, min_bytes, max_bytes)
```

Engine parameters:

- `database` – Database name. Instead of the database name, you can use a constant expression that returns a string.
- `table` – Table to flush data to.
- `num_layers` – Parallelism layer. Physically, the table will be represented as num_layers of independent buffers. Recommended value: 16.
- `min_time`, `max_time`, `min_rows`, `max_rows`, `min_bytes`, and `max_bytes` – Conditions for flushing data from the buffer.


Data is flushed from the buffer and written to the destination table if all the `min*` conditions or at least one max* condition are met.

- `min_time`, `max_time` – Condition for the time in seconds from the moment of the first write to the buffer.
- `min_rows`, `max_rows` – Condition for the number of rows in the buffer.
- `min_bytes`, `max_bytes` – Condition for the number of bytes in the buffer.


During the write operation, data is inserted to a num_layers number of random buffers. Or, if the data part to insert is large enough (greater than `max_rows` or `max_bytes`), it is written directly to the destination table, omitting the buffer.

The conditions for flushing the data are calculated separately for each of the num_layers buffers. For example, if `num_layers = 16` and `max_bytes = 100000000`, the maximum RAM consumption is 1.6 GB.

Example:

```sql
CREATE TABLE merge.hits_buffer AS merge.hits ENGINE = Buffer(merge, hits, 16, 10, 100, 10000, 1000000, 10000000, 100000000)
```

Creating a merge.hits_buffer table with the same structure as `merge.hits` and using the Buffer engine. When writing to this table, data is buffered in RAM and later written to the `merge.hits` table. 16 buffers are created. The data in each of them is flushed if either 100 seconds have passed, or one million rows have been written, or 100 MB of data have been written; or if simultaneously 10 seconds have passed and 10,000 rows and 10 MB of data have been written. For example, if just one row has been written, after 100 seconds it will be flushed, no matter what. But if many rows have been written, the data will be flushed sooner.

When the server is stopped, with `DROP TABLE` or `DETACH TABLE`, buffer data is also flushed to the destination table.

You can set empty strings in single quotation marks for the database and table name. This indicates the absence of a destination table. In this case, when the data flush conditions are reached, the buffer is simply cleared. This may be useful for keeping a window of data in memory.

When reading from a Buffer table, data is processed both from the buffer and from the destination table (if there is one).

Note that the Buffer tables does not support an index. In other words, data in the buffer is fully scanned, which might be slow for large buffers. (For data in a subordinate table, the index that it supports will be used.)

If the set of columns in the Buffer table doesn’t match the set of columns in a subordinate table, a subset of columns that exist in both tables is inserted.

If the types don’t match for one of the columns in the Buffer table and a subordinate table, an error message is entered in the server log and the buffer is cleared.
The same thing happens if the subordinate table doesn’t exist when the buffer is flushed.

If you need to run `ALTER` for a subordinate table and the Buffer table, we recommend first deleting the Buffer table, running `ALTER` for the subordinate table, then creating the Buffer table again.

If the server is restarted abnormally, the data in the buffer is lost.

`FINAL` and `SAMPLE` do not work correctly for Buffer tables. These conditions are passed to the destination table, but are not used for processing data in the buffer. If these features are required we recommend only using the Buffer table for writing, while reading from the destination table.

When adding data to a Buffer, one of the buffers is locked. This causes delays if a read operation is simultaneously being performed from the table.

Data that is inserted to a Buffer table may end up in the subordinate table in a different order and in different blocks. Because of this, a Buffer table is difficult to use for writing to a `CollapsingMergeTree` correctly. To avoid problems, you can set `num_layers` to 1.

If the destination table is replicated, some expected characteristics of replicated tables are lost when writing to a Buffer table. The random changes to the order of rows and sizes of data parts cause data deduplication to quit working, which means it is not possible to have a reliable ‘exactly once’ write to replicated tables.

Due to these disadvantages, we can only recommend using a Buffer table in rare cases.

A Buffer table is used when too many `INSERT`s are received from a large number of servers over a unit of time and data can’t be buffered before insertion, which means the `INSERT`s can’t run fast enough.

Note that it doesn’t make sense to insert data one row at a time, even for Buffer tables. This will only produce a speed of a few thousand rows per second, while inserting larger blocks of data can produce over a million rows per second (see the section “Performance”).

### External Data for Query Processing 

ClickHouse allows sending a server the data that is needed for processing a query, together with a `SELECT` query. This data is put in a temporary table (see the section “Temporary tables”) and can be used in the query (for example, in `IN` operators).

For example, if you have a text file with important user identifiers, you can upload it to the server along with a query that uses filtration by this list.

If you need to run more than one query with a large volume of external data, don’t use this feature. It is better to upload the data to the DB ahead of time.

External data can be uploaded using the command-line client (in non-interactive mode), or using the HTTP interface.

In the command-line client, you can specify a parameters section in the format

```
--external --file=... [--name=...] [--format=...] [--types=...|--structure=...]
```

You may have multiple sections like this, for the number of tables being transmitted.

- `--external` - Marks the beginning of a clause.
- `--file` - Path to the file with the table dump, or -, which refers to stdin.
Only a single table can be retrieved from stdin.

The following parameters are optional: 
- `--name` - Name of the table. If omitted, _data is used.
- `--format` - Data format in the file. If omitted, TabSeparated is used.

One of the following parameters is required:
- `--types` - A list of comma-separated column types. For example: UInt64,String. The columns will be named _1, _2, …
- `--structure` - The table structure in the formatUserID UInt64, URL String. Defines the column names and types.

The files specified in ‘file’ will be parsed by the format specified in ‘format’, using the data types specified in ‘types’ or ‘structure’. The table will be uploaded to the server and accessible there as a temporary table with the name in ‘name’.

Examples:

```shell
$ echo -ne "1\n2\n3\n" | clickhouse-client --query="SELECT count() FROM test.visits WHERE TraficSourceID IN _data" --external --file=- --types=Int8
849897
$ cat /etc/passwd | sed 's/:/\t/g' | clickhouse-client --query="SELECT shell, count() AS c FROM passwd GROUP BY shell ORDER BY c DESC" --external --file=- --name=passwd --structure='login String, unused String, uid UInt16, gid UInt16, comment String, home String, shell String'
/bin/sh 20
/bin/false      5
/bin/bash       4
/usr/sbin/nologin       1
/bin/sync       1
```

When using the HTTP interface, external data is passed in the multipart/form-data format. Each table is transmitted as a separate file. The table name is taken from the file name. The `query_string` is passed the parameters `name_format`, `name_types`, and `name_structure`, where name is the name of the table that these parameters correspond to. The meaning of the parameters is the same as when using the command-line client.

Example:

```shell
$ cat /etc/passwd | sed 's/:/\t/g' > passwd.tsv

$ curl -F 'passwd=@passwd.tsv;' 'http://localhost:8123/?query=SELECT+shell,+count()+AS+c+FROM+passwd+GROUP+BY+shell+ORDER+BY+c+DESC&passwd_structure=login+String,+unused+String,+uid+UInt16,+gid+UInt16,+comment+String,+home+String,+shell+String'
/bin/sh 20
/bin/false      5
/bin/bash       4
/usr/sbin/nologin       1
/bin/sync       1
```

For distributed query processing, the temporary tables are sent to all the remote servers.

### GenerateRandom Table Engine 

The GenerateRandom table engine produces random data for given table schema.

Usage examples:

- Use in test to populate reproducible large table.
- Generate random input for fuzzing tests.

#### Usage in ClickHouse Server 

```sql
ENGINE = GenerateRandom(random_seed, max_string_length, max_array_length)
```

The `max_array_length` and `max_string_length` parameters specify maximum length of all
array columns and strings correspondingly in generated data.

Generate table engine supports only `SELECT` queries.

It supports all DataTypes that can be stored in a table except `LowCardinality` and `AggregateFunction`.

#### Example 

1. Set up the generate_engine_table table:

```sql
CREATE TABLE generate_engine_table (name String, value UInt32) ENGINE = GenerateRandom(1, 5, 3)
```

2. Query the data:

```sql
SELECT * FROM generate_engine_table LIMIT 3
┌─name─┬──────value─┐
│ c4xJ │ 1412771199 │
│ r    │ 1791099446 │
│ 7#$  │  124312908 │
└──────┴────────────┘
```
