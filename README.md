# Assorted ClickHouse Administrator's Utilities

Actually *not* inspired by [pg-diff](https://michaelsogos.github.io/pg-diff)

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

* CLUSTERS file line format: ALIAS HOSTNAME (will add port 9000)
* ALIAS is short name to display in summary
* HOSTNAME to execute query on
* QUERY returns rows with value in first column (`origin' alias available)
* comparison across common keys (second column onwards) a la INNER JOIN
* TOLERANCE specifies max allowed percentage difference from mean of all clusters
* result is maximum absolute difference across all rows
* output format: GOOD|BAD<TAB>SUMMARY<TAB>DETAIL

Example:

```shell
data-diff ./clusters "SELECT sum(temperature), time FROM temperatures"
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
