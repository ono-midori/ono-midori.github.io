# The Application and Performance of Clickhouse
- [The Application and Performance of Clickhouse](#the-application-and-performance-of-clickhouse)
  - [Introduction](#introduction)
    - [Distinctive Featires of ClickHouse](#distinctive-featires-of-clickhouse)


## Introduction

ClickHouse is a column-oriented database management system (DBMS) for online analytical processing of queries (OLAP). In a column-oriented DMBS, the values from different columns are stored separately, and data from the same column is stored together.

### Distinctive Featires of ClickHouse

- True Column-Oriented Database Management System. In such DBMS, no extra data is stored with the values. Among other things, this means that constant-length values must be supported, to avoid storing their length "number" next to the values. (定长数据结构). 
- Data Compression. Data compression does play a key role in achieving excellent performance. In addition to effecient general-purpose compression codecs with different trade-offs between disk space and CPU consumption, ClickHouse provides specialized codecs for specific kinds of data.
- Disk Storage of Data. Keeping data physically sorted by primary key makes it possible to extract data for its specific values or value ranges with low latency, less than a few dozen milliseconds. ClickHouse is designed to work on regular hard drives.
- Paralle Processing on Multiple Cores. Large queries are parallelized naturally, taking all the necessary resources available on the current server.
- Vector Computation Engine. Data is not only stored by columns but is processed by vectors (parts of columns), which allows achieving high CPU efficiency.

