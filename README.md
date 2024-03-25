# Assorted ClickHouse Administrator's Utilities

### Common Prerequisites

ClickHouse Driver library, eg:

```shell
pip3 install clickhouse_driver
```

## Active Tables

Usage:

```shell
./active-tables clickhouse.local
```

Prints out data flows of recent insert queries based on Buffer engine and
Materialized View dependencies

## Data Diff

Usage:

```shell
data-diff CLUSTERS QUERY [TOLERANCE]
```

* CLUSTERS file line format: SHORT-NAME BOOTSTRAP-HOSTNAME (will add port 9000)
* TOLERANCE specifies max allowed percentage deviation from mean of all clusters
* QUERY returns a number and can use `origin' time fixed between clusters
* output format: GOOD|BAD\<TAB\>SUMMARY\<TAB\>VALUES

Example:

```shell
data-diff ./clusters ./tables
```

Further work: replace with
[Datafold data-diff](https://github.com/datafold/data-diff)

## SQL Diff

Usage:

```shell
sql-diff HOST FILE ...
```

* One or more FILEs contain CREATE statements to diff against
* HOST without port (will use 9000)

Example:

```shell
sql-diff clickhouse.local tables/*.sql
```

Inspired by ClickHouse PlantUML

Limitations:

Almost any ClickHouse feature you can imagine is unsupported, you have been
warned. With that said, the little there is has proven useful.
