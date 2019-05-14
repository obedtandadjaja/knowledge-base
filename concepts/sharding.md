# Sharding / Data Partitioning

Sharding is important for redundancy, availability, durability, and horizontal scalability.

**Redundancy + Availability**: Multiple shards host the same data and so multiple shards can serve multiple requests for the same data

**Durability**: When one shard is corrupted then there is more copy in other shards

**Horizontal Scalability**: As the amount of data grows, we just need more shards to handle the load instead of keep increasing storage size of a single machine

![picture](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/1_QDRlAtQaXkniqJboucKOVg.png?raw=true)

## Partitioning methods

### Horizontal partitioning
  - Range based sharding.
  - Put different rows into different tables.
  - Cons:
    - Range must be chosen carefully, if not it will lead to unbalanced servers
    
### Vertical partitioning
  - Divide data for a specific feature to their own server.
  - Pros:
    - Straightforward to implement.
    - Low impact on the application.
  - Cons:
    - To support growth of the application, a database may need further partitioning.
    
### Directory-based partitioning
  - A lookup service that knows the partitioning scheme and abstracts it away from the database access code.
  - Allow addition of db servers or change of partitioning schema without impacting application.
  - Cons:
    - Can be a single point of failure.

## Partitioning criteria

### Key or hash-based partitioning

- Apply a hash function to some key attribute of the entry to get the partition number.
- Problem:
  - Adding new servers may require changing the hash function, which would need redistribution of data and downtime for the service.
- Workaround: [consistent hashing](https://en.wikipedia.org/wiki/Consistent_hashing).

### List partitioning
- Each partition is assigned a list of values.

### Round-robin partitioning
- With `n` partitions, the `i` tuple is assigned to partition `i % n`.

### Composite partitioning
- Combine any of above partitioning schemes to devise a new scheme.
- Consistent hashing is a composite of hash and list partitioning.
  - Key -> reduced key space through hash -> list -> partition.

## Common problems of sharding

Most of the constraints are due to the fact that operations across multiple tables or multiple rows in the same table will no longer run on the same server.

### Joins and denormalization
  - Joins will not be performance efficient since data has to be compiled from multiple servers.
  - Workaround: denormalize the database so that queries can be performed from a single table. But this can lead to data inconsistency.
  
### Referential integrity
  - Difficult to enforce data integrity constraints (e.g. foreign keys).
  - Workaround
    - Referential integrity is enforced by application code.
    - Applications can run SQL jobs to clean up dangling references.
    
### Rebalancing
  - Necessity of rebalancing
    - Data distribution is not uniform.
    - A lot of load on one shard.
  - Create more db shards or rebalance existing shards changes partitioning scheme and requires data movement.
