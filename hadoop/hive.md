# Hive

Stores schema in a database and processed data into HDFS. Basically treats an HDFS just like SQL.

![picture](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/hive_architecture.jpg?raw=true)

**UI** - There is a web UI
**Metastore** - stores the metadata of tables, databases, columns in a table, their data types and HDFS mapping. This is very similar to Postgres' information_schema table
**HiveQL Process Engine** - Instead of doing MapReduce we utilize Metastore to create QL
**Execution Engine** - Processes the QL and generates results just like MapReduce results

## Execution Steps

1. Execute query from command line or web UI
2. Get plan from driver - the driver will use query compiler to parse the query and come up with the query plan
3. Driver asks for metadata from Metastore
4. Metastore sends metadata back to compiler
5. Compiler checks the requirements and sends the plan back to the driver
6. Driver executes the plan to the execution engine
7. Execute the job in Hadoop server, sends job to JobTracker which is in Name node and assigns the job to TaskTracker which is in Data node
8. Fetches the result from ecxecution engine which subsequently gets it from Data nodes
9. Sends the result back to the driver
10. Driver sends the result back to the interface

## Types

All the usuals plus the following:

- **Union** - Essentially a struct, e.g. `UNIONTYPE<int, double, array<string>, struct<a:int,b:string>>`
- Missing values are represented by special value `NULL`
- **Arrays** - e.g. `ARRAY<data_type>`
- **Maps** - e.g. `MAP<primitive_type, data_type>`
- **Struct** - e.g. `STRUCT<col_name : data_type [COMMENT col_comment], ...>` basically union with the ability to comment

## Create Table

Syntax:
```
CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.] table_name

[(col_name data_type [COMMENT col_comment], ...)]
[COMMENT table_comment]
[ROW FORMAT row_format]
[STORED AS file_format]
```

## Alter Table

Syntax:
```
ALTER TABLE name RENAME TO new_name
ALTER TABLE name ADD COLUMNS (col_spec[, col_spec ...])
ALTER TABLE name DROP [COLUMN] column_name
ALTER TABLE name CHANGE column_name new_name new_type
ALTER TABLE name REPLACE COLUMNS (col_spec[, col_spec ...])
```

## Drop Table

Syntax:
```
DROP TABLE [IF EXISTS] table_name;
```

## Partition Table

Syntax:
```
ALTER TABLE table_name ADD [IF NOT EXISTS] PARTITION partition_spec
[LOCATION 'location1'] partition_spec [LOCATION 'location2'] ...;

partition_spec:
: (p_column = p_col_value, p_column = p_col_value, ...)
```

Example:
```
hive> ALTER TABLE employee
> ADD PARTITION (year=’2012’)
> location '/2012/part2012';
```

## Relational Operators

All the usual ones plus the following:

- **RLIKE** - NULL if A or B is null, true if any substring of A matches the regex of B, otherwise false

## Complex Operators

- **A[n]** - returns the nth element in the array A
- **M[key]** - returns the value corresponding to the key in the map
- **S.x** - returns the field of S (struct only)

## Built-in Functions

All the usual ones plus the following:

- **rand** - randomize a number with exclusion
- **from_unixtime** - converts epoch time to a string representation of the timestamp
- **get_json_object** - extracts json object from json string

## Views

Syntax:
```
CREATE VIEW [IF NOT EXISTS] view_name [(column_name [COMMENT column_comment], ...) ]
[COMMENT table_comment]
AS SELECT
```

## Index

Syntax:
```
CREATE INDEX index_name
ON TABLE base_table_name (col_name, ...)
AS 'index.handler.class.name'
[WITH DEFERRED REBUILD]
[IDXPROPERTIES (property_name=property_value, ...)]
[IN TABLE index_table_name]
[PARTITIONED BY (col_name, ...)]
[
   [ ROW FORMAT ...] STORED AS ...
   | STORED BY ...
]
[LOCATION hdfs_path]
[TBLPROPERTIES (...)]
```
