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
Each table or index whose size is less than 1GB is a single file stored under the dtabase directory it belongs to. These files are managed by the variable, `relfilenode`. The relfilenode values of tables and indexes typically math its respective OIDs.

The `relfilenode` values are changed by some commands (TRUNCATE, REINDEX, CLUSTER).

When the file size of tables and indexes exceed 1GB, PostgreSQL creates a new file named like `{relfilenode}.1` and uses it.

Each table has 2 associated files suffixed with '\_fsm' and '\_vm'. These are referred to as **free space map** and **visibility map**, storing the information of the free space capacity and the visibility on each page within the table file. Indexes only have free space maps and don't have visibility map.

### Tablespaces
A tablespace is an additional data area outside the base directory. A tablespace is created under the directory specified when you issue `CREATE TABLESPACE` statement, and under that directory, the version-specific subdirectory will be created.

The tablespace directory is addressed by a symbolic link from the `pg_tblspc` subdirectory, and the link name is the same as the OID value of the tablespace.

If you create a new dtabase under the tablespace, its directory is created under the version-specific subdirectory.

Tablespace allow database administrators to define locations in the file system where the files representing database objects can be stored. This is useful in at least 2 ways: First, if the partition runs out of sapce and cannot be extended, a tablespace can be created on a different partition and used until the system can be configured. Second, tablespaces allow an administrator to use knowledge of the usage pattern of database objects to optimize performance. For example, an index which is very heavily used can be placed on a very fast, highly available disk, such as an expensive solid state device.

### Internal Layout of a Heap Table File


## Multiversion concurrency control (MVCC)
PostgreSQL manages concurrency through MVCC which gives each transaction a snapshot of the database, allowing changes to be made without being visible to other transactions until the changes are committed. This largely eliminates the need for read locks, and ensures the database maintains the ACID principles in an efficient manner.

PostgreSQL offers 3 levels of transaction isolation:

1. Read Committed
2. Repeatable Read
3. Serializable

Because PostgreSQL is immune to dirty reads, requesing a Read Uncommitted transaction isolation level provides read committed instead.

PostgreSQL supports full serializability via serializable snapshot isolation (SSI) technique.
