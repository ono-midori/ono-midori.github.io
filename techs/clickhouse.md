# The Application and Performance of Clickhouse
- [The Application and Performance of Clickhouse](#the-application-and-performance-of-clickhouse)
  - [Introduction](#introduction)
    - [Distinctive Featires of ClickHouse](#distinctive-featires-of-clickhouse)
  - [Table Engines](#table-engines)
    - [MergeTree](#mergetree)
      - [Main Features](#main-features)
      - [Creating a Table](#creating-a-table)
      - [Data Storage](#data-storage)
      - [Primary Keys and Indexes in Queries](#primary-keys-and-indexes-in-queries)
      - [Selecting the Primary Key](#selecting-the-primary-key)
      - [Choosing a Primary Key that Differs from the Sorting Key](#choosing-a-primary-key-that-differs-from-the-sorting-key)
      - [Using Multiple Block Devices for Data Storage](#using-multiple-block-devices-for-data-storage)
      - [Custom Partitioning Key](#custom-partitioning-key)


## Introduction

ClickHouse is a column-oriented database management system (DBMS) for online analytical processing of queries (OLAP). In a column-oriented DMBS, the values from different columns are stored separately, and data from the same column is stored together.

### Distinctive Featires of ClickHouse

- True Column-Oriented Database Management System. In such DBMS, no extra data is stored with the values. Among other things, this means that constant-length values must be supported, to avoid storing their length "number" next to the values. (定长数据结构). 
- Data Compression. Data compression does play a key role in achieving excellent performance. In addition to effecient general-purpose compression codecs with different trade-offs between disk space and CPU consumption, ClickHouse provides specialized codecs for specific kinds of data.
- Disk Storage of Data. Keeping data physically sorted by primary key makes it possible to extract data for its specific values or value ranges with low latency, less than a few dozen milliseconds. ClickHouse is designed to work on regular hard drives.
- Paralle Processing on Multiple Cores. Large queries are parallelized naturally, taking all the necessary resources available on the current server.
- Vector Computation Engine. Data is not only stored by columns but is processed by vectors (parts of columns), which allows achieving high CPU efficiency.

## Table Engines

The table engin determines:
- How and where data is stored, where to write it to, and where to read it from
- Concurrent data access
- Use of indexes
- Whether multithreaded request execution is possible

### MergeTree

The `MergeTree` engine and other engines of this family are the most robust ClickHouse table engines. They are designed for inserting a very large amount of data into a table. The data is quickly written to the table part by part, the rules are applied for merging the parts in the background. This method is much more efficient than continually rewriting the data in storage during insert.

#### Main Features

- Stores data sorted by primary key.
- Partitions can be used if partitioning key is specified. ClickHouse supports certain operations with partitions that are more effective than general operations on the same data with the same result. ClickHouse also automatically cuts off the partition data where the partitioning key is specified in the query.

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
[TTL expr [DELETE|TO DISK 'xxx'|TO VOLUME 'xxx'], ...]
[SETTINGS name=value, ...]
```

- `ENGINE`: Name and parameters of the engine.
- `ORDER BY`: The sorting key. ClickHouse uses the sorting key as a primary key if the primary key is not defined obviously by the `PRIMARY KEY` clause.
- `PARTITION BY`: The partitioning key.
- `PRIMARY KEY`: The primary key if it differs from the sorting key.

#### Data Storage

A table consists of data parts sorted by primary key.

When data is inserted in a table, separate data parts are created, and the data in each part is lexicographically sorted by primary key. Data belonging to different partitions are separated into different parts. In the background, ClickHouse merges data parts for more efficient storage. Parts belonging to different partitions are not merged. The merge mechanism does not guarantee that all rows with the same primary key will be in the same data part.

Data parts can be stored in `Wide` or `Compact` format. In `Wide` format, each column is stored in a separate file in a filesystem. In `Compact` format, all columns in a table are stored in one file. `Compact` format can be used to increase performance of small and frequenct inserts (因为读取的文件更少). If the number of bytes in a data part is less than `min_bytes_for_wide_part`, or if the number of rows in a data part is less than `min_rows_for_wide_part`, the part is stored in `Compact` format. Otherwise it is stored in `Wide` format. If none of these settings is set, data parts are stored in `Wide` format.

Each data part is logically divided into granules. A granule is the smallest indivisible data set that ClickHouse reads when selecting data. ClickHouse doesn't split a row or a value, so each granule always contains an integer number of rows. The first row of a granule is marked with the value of the primary key for the row. For each data part, ClickHouse creates an index file that stores the marks (就是primary keys). For each column, whether it's in the primary key or not, ClickHouse also stores the same marks. These marks let you find data directly in column files. 

The granule size is restricted by the `index_granularity` and `index_granularity_bytes` settings of the table engine. The number of rows in a granule lays in the `[1, index_granularity]` range, depending on the size of the rows. The size of a granule can exceed `index_granularity_bytes` if the size of a single row is greater than the value of the settings (一行的大小都比预设值大，则存一行). In this case, the size of granule equals to the size of the row.

#### Primary Keys and Indexes in Queries

Taking the `CounterID, Date)` primary key as an example, the sorting and index can be illustrated as follows. The `CounterID` is a character, and the `Date` is represented by a single digit.

```
  Whole data:     [---------------------------------------------]
  CounterID:      [aaaaaaaaaaaaaaaaaabbbbcdeeeeeeeeeeeeefgggggggghhhhhhhhhiiiiiiiiikllllllll]
  Date:           [1111111222222233331233211111222222333211111112122222223111112223311122333]
  Marks:           |      |      |      |      |      |      |      |      |      |      |
                  a,1    a,2    a,3    b,3    e,2    e,3    g,1    h,2    i,1    i,3    l,3
  Marks numbers:   0      1      2      3      4      5      6      7      8      9      10
```

If the data query specfies:
- `CounterID in ('a', 'h')`, the server reads the data in the range of marks `[0, 3)` (也就是0,1,2) and `[6, 8)`(也就是6,7).
- `CounterID in ('a', 'h') AND Date = 3`, the server reads the data in the ranges of marks `[1, 3)` and `[7, 8)`.
- `Date = 3`, the server reads the data in the range of marks `[1, 10]`.

The examples above show that it is always more effective to use an index than a full scan. 

#### Selecting the Primary Key

ClickHouse does not require a unique primary key. You can insert multiple rows with the same primary key. The number of columns in the primary key is not explicitly limited. Depending on the data structure, you can include more or few columns in the primary key, and this may:

- Improve the performance of an index. 主键的列越多，搜索范围越小.
- Improve the data compression. ClickHouse sorts data by primary key, so the higher the consistency, the better the compression.

You can create a table without a primary key using the `ORDER BY tuple()` syntax(填空元组). In this case, ClickHouse stores data in the order of inserting. If you want to save data order when inserting data by `INSERT ... SELECT` queries, set `max_insert_threads = 1`. To select data in the initial order, use single-threaded `SELECT` queries.

#### Choosing a Primary Key that Differs from the Sorting Key

The primary key is an expression with values that are written in the index file for each mark, and the sorting key is an expression for sorting the rows in data parts. They can be specified as different.

#### Using Multiple Block Devices for Data Storage

`MergeTree` family table engines can store data on multiple block devices. For example, it can be useful when the data of a certain table are implicitly split into "hot" and "cold". The most recent data is regularly requested but requires only a small amount of space. On the contrary, the fat-tailed historical data is requested rarely. If several disks are available, the “hot” data may be located on fast disks (for example, NVMe SSDs or in memory), while the “cold” data - on relatively slow ones (for example, HDD).

#### Custom Partitioning Key

Partitioning is available for the MergeTree family tables. A partition is a local combination of records in a table by a specified criterion. Each partition is stored separately to simply manipulations of this data. When accessing the data, ClickHouse uses the smallest subset of partitions possible (就是把最小数量的partition取出来).

The partition is specified in the `PARTITION BY expr` clause when creating a table. The partition key can be any expression from the table columns, and can also be a tuple of expressions (similar to the primary key).

*When inserting new data to a table, this data is stored as a separate part (chunk) sorted by the primary key. In 10~15 minutes after inserting, the parts of the same partition are merged into the entire part.*

Note that a merge only works for data parts that have the same value for the partitioning expression. This means you shouldn't make overly granular partitions (不能分的太细). Otherwise, the `SELECT` query performs poorly because of an unreasonably large number of files in the file system and open file descriptprs.

Use the **system.parts** table to view the table parts and partitions.
