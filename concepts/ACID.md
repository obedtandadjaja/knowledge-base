# ACID

ACID (Atomicity, Consistency, Isolation, Durability) is a set of properties of database transactions intended to guarantee validity even in the event of errors and failures.

ACID is a property commonly found in relational databases.

### Atomicity

Guarantees that each transaction is treated as a single unit, which either succeeds completely, or fails completely.

### Consistency

Guarantees that the data will be consistent; none of the constraints and validations set will ever be violated.

### Isolation

Ensures that concurrent execution of transactions leaves the database in the same state that would have been obtained if the transactions were executed sequentially. The effects of an incomplete transaction should not be visible to other transactions.

### Durability

Guarantees that once a transaction has been committed, it will remain committed even in the case of a system failure. Completed transactions are recorded in non-volatile memory.
