+++ 
draft = false
date = 2025-03-18T18:36:53-04:00
title = "SQL Optimizations"
description = ""
slug = "sqldb-optimizations"
authors = ["Aditya Naidu"]
tags = ["sql", "rdbms", "optimization"]
categories = []
externalLink = ""
series = []
+++

A quick guide to optimizing Relational databases that solve most common performance issues. Examples use Postgresql but the general ideas are applicable to any Relational DB.


# Adding INDEXES

The default and most extensively used index type in Postgresql is B-Tree due to its versatility. If used correctly it can help in many of the general cases e.g. when WHERE clause has <, >, =, >=, <=, range operators. It can also speed up ORDER BY clauses.

There are other index types that are useful in special cases like GiST, Hash,
GIN, etc. We will not discuss these. More information on them [here](https://www.postgresql.org/docs/current/indexes-types.html)

## Pros and Cons of Indexes
PROS: Faster SELECT. Indexes can help with DMLs (INSERT, UPDATE, DELETE) with WHERE clauses just like SELECTs.
CONS: Possibly slower DMLs since the index also needs to be written to and maintained.

## EXAMPLES

```sql
SELECT *
FROM users
WHERE u_first_name = ?
```
In the example above, an index on column u_first_name should work. So

```sql
CREATE INDEX users_first_name_idx ON users (u_first_name);
```

Now lets take a bit more involved example. Below are three where clauses used on
the same table.
```sql
WHERE u_first_name = ? AND u_last_name = ? AND u_department = ?
WHERE u_first_name = ? AND u_last_name = ?
WHERE u_first_name = ?
```
In this case a single composite index (single index on multiple columns) will work for all three
```sql
CREATE INDEX user_first_last_dept_idx ON users(
    u_first_name, u_last_name, u_department);
```
In fact this index could be used for WHERE clauses that have first_name along with any other columns. Conversely, this index won't be used when WHERE is missing u_first_name column.

Take another example
```sql
WHERE u_first_name = ? AND u_last_name = ?
```

In this example two separate indexes on u_first_name and u_last_name could also
be used but might be inefficient compared to one composite index on u_first_name
and u_last_name combined. In case of separate index, postgres will scan both
indexes to find matching rows for u_first_name or u_last_name. Then postgres will
do a bitmap AND (if the operator in WHERE is OR it will be a bitmap OR) of the two sets. See [here](https://www.postgresql.org/docs/17/indexes-bitmap-scans.html) for more details. If the 

With numerious way to improve performance, how do we know which query is slow
and which exact INDEXES to create to improve performance? This is where slow query logs and EXPLAIN comes in.


# Slow query logs
In AWS RDS (or any other) Postgres setup, set the log_min_duration_statement to a value in milliseconds. Any SQL taking longer than the set value will be logged in the server logs. Finding slow queries using this setup will be much faster than making changes to all application code.


# EXPLAIN [ANALYZE]

The EXPLAIN statement will show the query planner output without running the query. Using EXPLAIN ANALYZE will run the query and give the actual run time. Once you find the query to be optimized, first try to eliminate any sequencial scans in the EXPLAIN output. This can generally be done by adding indexes based on the WHERE clause.

Sometimes the query planner output is unexpected like
* not using an index that could be used
* using two indexes that is suboptimal compared to using a single composite index.

This happens since your knowledge of data is different from what postgres knows about the data. This strongly suggest outdated statistics collected on the table by postgres. In such cases an ANALYZE of the table might fix the unexpected, suboptimal output of the planner. By default auto ANALYZE is on. But its possible that the auto ANALYZE frequency is not able to keep up with the volume of changes happening on the table.

For more information on EXPLAIN [see](https://www.postgresql.org/docs/current/sql-explain.html) and on ALYZE [see](https://www.postgresql.org/docs/current/sql-analyze.html)

# Removing INDEXES
Over time table indexes are created with the assumption that it will be needed by some query. Sometimes this assumption turns out to be wrong. In such cases these INDEXes just add overhead to DMLs and consume unnecessary space (read as cost in cloud deployments!) without giving any performance boosts. Such indexes need to be found and dropped.

To find unused indexes see [this](https://dmitry-naumenko.medium.com/how-to-define-unused-indexes-in-postgresql-471da6f6f33f)


# Bulk Insert

Use COPY command and then create all required indexes. If possible, use parallel COPY commands using multiple psql commands.

# Table partitioning

Table partitioning must be used only for very large tables since it adds overhead of its own. "very large" depends on the specific scenario. Table partitioning will reduce the number of indexes needed since the partition logic itself creates a coarse index.

Multiple partitions of a single partitioned table can be populated in parallel using a COPY command for each partition. This can speed up table population.
