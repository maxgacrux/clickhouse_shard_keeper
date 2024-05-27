# SQL Script for Distributed Table Creation and Data Insertion

This script creates and populates a distributed table using ClickHouse. It includes the creation of a distributed table and a MergeTree table on a cluster, followed by inserting data and performing a select query.

## Quick Start

To quickly start the setup, use the following command:

```sh
docker-compose up --build
```

## Step 1: Create Distributed Table

```sql
CREATE TABLE my_distributed_table
(
    id UInt64,
    name String
)
ENGINE = Distributed('cluster_1', 'test', 'my_table', sipHash64(id));
```

This statement creates a distributed table named `my_distributed_table` with two columns: `id` of type `UInt64` and `name` of type `String`. The table uses the `Distributed` engine to distribute data across the nodes in `cluster_1`, targeting the `my_table` table in the `test` database, using `sipHash64` on `id` for sharding.

## Step 2: Create MergeTree Table on Cluster

```sql
CREATE TABLE IF NOT EXISTS test.my_table ON CLUSTER 'cluster_1'
(
    id UInt64,
    name String
)
ENGINE = MergeTree()
ORDER BY id;
```

This statement creates a `MergeTree` table named `my_table` in the `test` database on the `cluster_1` cluster, with the same schema as the distributed table. The table uses the `MergeTree` engine and orders data by the `id` column.

## Step 3: Insert Data into Distributed Table

```sql
INSERT INTO my_distributed_table VALUES (1, 'John'), (2, 'Alice'), (3, 'Bob');
INSERT INTO my_distributed_table VALUES (4, 'John'), (5, 'Alice'), (6, 'Bob');
INSERT INTO my_distributed_table VALUES (7, 'John'), (8, 'Alice'), (9, 'Bob');
INSERT INTO my_distributed_table VALUES (10, 'John'), (11, 'Alice'), (12, 'Bob');
INSERT INTO my_distributed_table VALUES (13, 'John'), (14, 'Alice'), (15, 'Bob');
```

These statements insert multiple rows into the `my_distributed_table`. Each `INSERT` statement adds three records with different `id` values and corresponding `name` values.

## Step 4: Select Data from Distributed Table

```sql
SELECT * FROM my_distributed_table;
```

This statement retrieves all records from the `my_distributed_table`.

## Step 5: Select Data with Shard Information

```sql
SELECT id, name, shardNum() AS shard_num FROM cluster('cluster_1', 'test', 'my_table');
```

This statement retrieves `id` and `name` from the `my_table` table in the `test` database on `cluster_1`, along with the shard number for each record using the `shardNum()` function.
