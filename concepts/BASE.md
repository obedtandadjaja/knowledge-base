# BASE

BASE (Basically Available, Soft state, Eventual consistency). A model used in distributed computing to achieve high availability that guarantees that if no new update are made a given data item, eventually all accesses to that item will return the last updated value.

A BASE system gives up on Consistency (every read receives the most recent write or an error).

### Basically available

Indicates that the system guarantees availability. If a single node fails, part of the data won't be available, but the entire data layer stays operational.

### Soft state

Indicates that the state of the system may change over time, even without input. This is because of the eventual consistency model.

### Eventual consistency

Indicates that the system will become consistent over time, given that the system doesn't receive input during that time.
