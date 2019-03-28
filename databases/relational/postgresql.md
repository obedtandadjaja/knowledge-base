# PostgreSQL
PostgreSQL is an open source, object-relational database management system (ORDBMS) with an emphasis on extensibility and standards compliance. It can handle workloads ranging from samll single-machine applications to large Internet-facing applications (or for data warehousing) with many concurrent users.

PostgreSQL is ACID (Atomicity, Consistency, Isolation, Durability) compliant and transactional. PostgreSQL has updatable views and materialized views, triggers, foreign keys, supports functions and stored procedures and other expadability.

## Database Cluster, Databases, and Tables

### Logical Structure Database Cluster
A **database cluster** is a collection of databases managed by a PostgreSQL server. The term 'database cluster' in PostgreSQL does not mean 'a group of database servers'. A PostgreSQL server runs on a single host and manages a single database cluster.

![database_cluster](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/fig-1-01.png?raw=true)

A database is a collection of database objects. In the relational database theory, a database object is a data structure used either to store or to reference data.

All database objects in PostgreSQL are internally managed by object indentifiers (OIs), which are unsigned 4-byte integers. The relations between database objects and the respective OIDs are stored in system catalogs, depending on the type of objects (i.e. pg_database and pg_class).

### Physical Structure of Database Cluster
A database cluster basically is one directory referred to as **base directory**. Though it is not compulsory, the path of the base directory is usually set to the env variable `PGDATA`. It contains subdirectories and lots of files.

### Layout of a Database Cluster
![database_cluster_layout](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/Screen%20Shot%202019-01-22%20at%2010.49.59%20AM.png?raw=true)

### Layout of Databases
A database is a subdirectory under the base subdirectory; and the database directory names are identical to the respective OIDs.

### Layout of Files Associated with Tables and Indexes
Each table or index whose size is less than 1GB is a single file stored under the dtabase directory it belongs to. These files are managed by the variable, `relfilenode`. The relfilenode values of tables and indexes typically match its respective OIDs.

The `relfilenode` values are changed by some commands (TRUNCATE, REINDEX, CLUSTER).

When the file size of tables and indexes exceed 1GB, PostgreSQL creates a new file named like `{relfilenode}.1` and uses it.

Each table has 2 associated files suffixed with '\_fsm' and '\_vm'. These are referred to as **free space map** and **visibility map**, storing the information of the free space capacity and the visibility on each page within the table file. Indexes only have free space maps and don't have visibility map.

### Tablespaces
A tablespace is an additional data area outside the base directory. A tablespace is created under the directory specified when you issue `CREATE TABLESPACE` statement, and under that directory, the version-specific subdirectory will be created.

The tablespace directory is addressed by a symbolic link from the `pg_tblspc` subdirectory, and the link name is the same as the OID value of the tablespace.

If you create a new dtabase under the tablespace, its directory is created under the version-specific subdirectory.

Tablespace allow database administrators to define locations in the file system where the files representing database objects can be stored. This is useful in at least 2 ways: First, if the partition runs out of sapce and cannot be extended, a tablespace can be created on a different partition and used until the system can be configured. Second, tablespaces allow an administrator to use knowledge of the usage pattern of database objects to optimize performance. For example, an index which is very heavily used can be placed on a very fast, highly available disk, such as an expensive solid state device.

### Internal Layout of a Heap Table File
Inside the data file (heap table, index table, free space map, and visibility map), it is divided into **pages** of fixed length. The default is 8192 byte (8 KB). Those pages within each file are numbered sequentially from 0, and such numbers are called **block numbers**. If the file has been filled up, a new empty page is added to the end of the file to increase the file size.

![database_cluster](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/fig-1-04.png?raw=true)

A page within a table contains 3 kinds of data:

1. **heap tuple** - record data. They are stacked in order from the bottom of the page.
2. **line pointer** - 4 byte long and holds . apointer to each head tuple. It is also called an **item pointer**.
3. **header data** - allocated in the beginning of the page. It is 24 byte long and contains general information about the page. The major variables of the structure are:
    * pd_lsn - this variable stores the LSN of XLOG record written by the last change of this page. It is an 8-byte unsigned integer, related to the WAL (Write-Ahead Logging) mechanism.
    * pd_checksum - This variable stores the checksum value of this page.
    * pd_lower, pd_upper - pd_lower points to the end of line pointers, and pd_upper to the beginning of the newest heap tuple.
    * pd_special - for indexes. In the page within tables, it points to the end of the page.
  
An empty space between the end of line pointers and the beginning of the newest tuple is referred to as **free space** or **hole**.

To identify a tuple within the table, tuple identifier (TID) is internally used. A TID comprises a pair of values: the block number of the page and the offset number of the line pointer that points to the tuple. 

### Writing Heap Tuples
The pd_lower of the page points to the first line pointer, and both the line pointer and the pd_upper point to the first heap tuple. When the second tuple is inserted, it is placed after the first one. The second line pointer is pushed onto the first one, and it points to the second tuple. The pd_lower changes to point to the second line pointer, and the pd_upper to the second heap tuple.

Other header data within the page (e.g. pd_lsn, pg_checksum, pg_flag) are also rewritten to appropriate values.

### Reading Heap Tuples
There are two typical access methods: sequential scan and B-tree index scan

![Heap Tuples](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/fig-1-06.png?raw=true)

#### Sequential Scan
All tuples in all pages are sequentially read by scanning all line pointers in each page.

#### B-tree Index Scan
An index file contains index tuples, each of which is composed of an index key and a TID pointing to the target tuple. If the index tuple with the key that you are looking for has been found, PostgreSQL reads the desired heap tuple using the TID value.

### Indexes
They are auxiliary structures: each index can be deleted and recreated back from the information in the table. 

Not only do they speed up data access, they also serve to enforce some integrity constraints (like uniqueness).

Despite difference between types of indexes, each of them associates a key with table rows that contain this key. Each row is identified by TID (tuple id), which consists of the number of block in the file and the position of the row inside the block. And so using indexes we can know the TID we are looking for without scanning the entire table.

Important to understand that an index speeds up data access at a certain maintenance cost. For each operation on indexed data, indexes for that table need to be updated too in the same transaction. 

## Process Architecture
PostgreSQL is a client/server type relational database system with the multi-process architecture and runs on a single host.

A collection of multiple processes cooperatively managing one database cluster is usually referred to as a "PostgreSQL server":

- Postgres server process - parent of all processes related to database cluster management
- Backend process - handles all queries and statements issued by clients
- Background process - perform processes like VACUUM and CHECKPOINT for database maintenance
- Replication associated process - perform streaming replication
- Background worker process - can perform any background processing implemented by users

### Postgres server process
On start up, it allocates a shared memory area in memory, starts various background processes, starts replication processes and background processes if necessary, as well as waits for connection requests from clients. Whenever it receives a connection request from a client, it starts a backend process.

A postgres server process listens to one network port (default: 5432). Although more than one PostgreSQL server can be run on the same host, each server should be set to listen to different ports.

### Backend process
Handles all queries issued by one connected client. Communicates with the client via a single TCP connection and terminates when the client gets disconnected.

Only allowed to operate on one database so client must specify which database to connect.

PostgreSQL allows multiple clients to connect simultaneously; admin can reduce the number of connections from config parameter `max_connections` (default: 100).

## Memory Architecture
Can be classified into 2 broad categories: local memory area ane shared memory area.

### Local memory area
Each backend process allocates a local memory area for query processing; each area is divided into several sub-areas - whose sizes are either fixed or variable.

Sub-areas:
- work_mem - executor uses this area for sorting tuples by ORDER BY and DISTINCT operations, and for joining tables by merge-join and hash-join operations
- maintenance_work_mem - maintenance operations
- temp_buffers - for storing temporary tables

### Shared memory area
Allocated by a PostgreSQL server on start up. Divided into several fix-sized sub-areas.

Sub-areas:
- shared buffer pool - loads pages within tables and indexes from a persistent storage to here, and operates them directly
- WAL buffer - ensure that no data has been lost by server failures. Buffering area of the WAL data before writing to a persistent storage
- commit log - keeps the states of all transactions for concurrency control mechanism

## Query Processing

### Overview
![Query Processing](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/fig-3-01.png?raw=true)

### Parser
Generates a parse tree that can be read by subsystems from a SQL statement in plain text. It will return an error if there is a syntax error in the query. 

The parser does not check the semantics o an input query. For example, even if the query contains a table name that does not exist, the parser does not return an error. Semantic checks are done by the analyzer.

### Analyzer
Runs a semantic analysis of a parse tree generated by the parser and generates a query tree.

### Rewriter
Rewrites the query tree according to the rules stored in the `pg_rules` system catalog if necessary.

### Planner and executor
Planner receives a query tree and generates a (query) plan tree that can be processed by the executor most effectively. The plannier is based on pure cost-base optimization; it does not support rule-based optimization and hints. 

As in other RDBMS, the EXPLAIN command displays the plan tree itself.

## Multiversion concurrency control (MVCC)
PostgreSQL manages concurrency through MVCC which gives each transaction a snapshot of the database, allowing changes to be made without being visible to other transactions until the changes are committed. This largely eliminates the need for read locks, and ensures the database maintains the ACID principles in an efficient manner.

PostgreSQL offers 3 levels of transaction isolation:

1. Read Committed
2. Repeatable Read
3. Serializable

Because PostgreSQL is immune to dirty reads, requesing a Read Uncommitted transaction isolation level provides read committed instead.

PostgreSQL supports full serializability via serializable snapshot isolation (SSI) technique.

### How MVC works
Every transaction in postgres gets a transaction ID called `XID`. This includes single one statement transactions such as an insert, update, or delete, as well as explicitly wrapping a group of statements together via `BEGIN - COMMIT`.

Wehn a transaction starts, Postgres `increments` an `XID` and assigns it to the current transaction. Postgres stores this transaction information on every row in the system, which is used to determine whether a row is visible to the transaction or not.

When you insert a row, Postgres will store the `XID` of the row and call it `xmin`. Every row that has been committed and has an `xmin` that is less than the current transaction's `XID` is visible to the transaction. Until the transaction is committed, that row will not be visible to other transactions. Once it commits and other transactions get created, they will be able to view the new row because they satisfy the `xmin < XID` condition - and the transaction that created the row has completed.

A similar mechanis, occurs for DELETE and UPDATE, only in these cases Postgres stores an `xmax` value on each row in order to determine visibility.

![picture](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/457-imported-1443570195-457-imported-1443554663-34-original.jpg?raw=true)

What about two transactions updating the same row at the same time? Postgres supports 2 models that allow you to control how this situation should be handled.

The default: `READ COMMITTED` reads the row after the initial transaction has been completed and then executes the statement. It starts over if the row changed while it was waiting. If you issue an update with a where clause, the where clause will rerun after the initial transaction commits.

![picture](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/457-imported-1443570195-457-imported-1443554664-35-original.jpg?raw=true)

`SERIALIZABLE` on the other hand, throws an error when the row it is modifying has been modified by another transaction. It's up to the app to handle that error and try again.

![picture](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/457-imported-1443570195-457-imported-1443554664-36-original.jpg?raw=true)

### Disadvantages of MVCC
Because different transactions will have visibility to a different set of rows, Postgres needs to maintain potentially obsolete records. This is why an UPDATE actually creates a new row and why DELETE doesn't really remove the row: it merely marks it as deleted and sets the XID values appropriately. As transactions complete, there will be rows in the database that cannot possibly be visible to any future transactions.

Another problem that comes from MVCC is that transaction IDs can only ever grow so much - they are 32 bits and can only support aroound 4 billion transactions. Qhen the XID reaches its max, it will wraparound and start back at zero. Suddenly all rows appear to be in future transactions, and no new transactions would have visibility into those rows.

Both dead rows and transaction XID wraparound problem are solved with `VACUUM`. This should be routine maintenance, and Postgres comes with an auto_vacuum daemon that will run at a configurable frequency. It's important to keep an eye on this because different deployments will have different needs when it comes to vacuum frequency.
