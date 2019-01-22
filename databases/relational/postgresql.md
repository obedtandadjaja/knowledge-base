# PostgreSQL
PostgreSQL is an open source, object-relational database management system (ORDBMS) with an emphasis on extensibility and standards compliance. It can handle workloads ranging from samll single-machine applications to large Internet-facing applications (or for data warehousing) with many concurrent users.

PostgreSQL is ACID (Atomicity, Consistency, Isolation, Durability) compliant and transactional. PostgreSQL has updatable views and materialized views, triggers, foreign keys, supports functions and stored procedures and other expadability.

### Multiversion concurrency control (MVCC)
PostgreSQL manages concurrency through MVCC which gives each transaction a snapshot of the database, allowing changes to be made without being visible to other transactions until the changes are committed. This largely eliminates the need for read locks, and ensures the database maintains the ACID principles in an efficient manner.

PostgreSQL offers 3 levels of transaction isolation:

1. Read Committed
2. Repeatable Read
3. Serializable

Because PostgreSQL is immune to dirty reads, requesing a Read Uncommitted transaction isolation level provides read committed instead.

PostgreSQL supports full serializability via serializable snapshot isolation (SSI) technique.
