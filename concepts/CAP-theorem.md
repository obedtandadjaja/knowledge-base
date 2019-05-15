# CAP Theorem

CAP (Consistency, Availability, Partition Tolerance)

**Consistency**: Every read receives the most recent write or an error.

**Availability**: Every request receives a response that is not an error.

**Partition Tolerance**: System continues to operate despite an arbitrary number of messages being dropped (or delayed) by the network between nodes.

- CAP theorem implies that in the presence of a network parition, one has to choose between consistency and availability.
- CAP theorem is frequently misunderstood as if one has to choose to abandon one of the three guarantees at all times. In fact, the choice is between consistency and availability **ONLY** when a network partition or failure happens; at all other times, no trade-off has to be made

ACID databases chooses consistency over availability.

BASE systems chooses availability over consistency.
