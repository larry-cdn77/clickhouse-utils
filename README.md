# ClickHouse Administrator's Assorted Utilities

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

Actually *not* inspired by [pg-diff](https://michaelsogos.github.io/pg-diff)

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

## Copier

Leaves a lot to be desired but has proven its worth copying datasets in the
tens of TBs consisting of dozens diverse table schemas

I like to run copying on the actual cluster nodes using the following screen
SSH invoker:

```zsh
#!/bin/zsh
# gotcha: screen logfile CRLF terminated
if [[ $# -lt 3 || $1 == --help ]] ; then
  print "usage: $0 [USER@]HOST SESSION CMD ..."
  print "       start any CMD in screen on HOST under SESSION"
  print "       log file set to /tmp/SESSION.log"
  print "       show log after 15 seconds"
  print "       script executed as Bourne shell at the moment"
  print "       fivefold parentheses escape: eg \x5c\x5c\x5c\x5c\x5c\("
  exit 2
fi
host=$1
session=$2
shift 2
print ${@} | ssh $host \
  cat \> /tmp/$session.sh \; \
  if screen -ls \| grep -q $session \; then \
    echo \'$session\' session exists \; exit 1 \; \
  fi \; \
  truncate -s0 /tmp/$session.log \; \
  \( set -x \; \
    screen -S $session -L -Logfile /tmp/$session.log -dm sh /tmp/$session.sh \; \
    sleep 15 \
  \) \; \
  cat /tmp/$session.log
```

For example (having set up .copier and copier files on each node):

```sh
screen-ssh bob.local my_table for d in 2025-11-22 2025-11-21 \; do \
  ./copier alice.local localhost my_table my_table t sum\\\\\(x\\\\\) 0.01 \$d \
  \; done && echo \\nmy_table success
```
