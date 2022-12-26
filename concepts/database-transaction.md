# Database Transactions

Popular in relational databases for users to do ACID-compliant mutations to the database.

## Glossary

* Dirty read: Situation where a transaction reads data that has not been committed
* Non repeatable read: Situation where 2 or more reads differ because an update occurred in between
* Phantom read: Occurs when 2 same queries are executed, but the rows retrieved are different

## Levels

4 isolation levels

1. Read uncommitted - lowest isolation level.

Transactions are not isolated from each other. Allow for other transactions to do dirty reads from the other transaction

2. Read committed.

Guarantees that any data read is committed at the moment it is read. The transaction holds a read or write lock on the current row, and tthus prevent other transactions from reading, updating, or deleting them.

No more dirty reads.

3. Repeatable read.

Transaction holds read locks on all rows it reference and writes locks on all rows it inserts, updates, or deletes.

No more repeatable reads.

4. Serializable.

All transaction executions appear to be serially executing.

No more phantom reads.
