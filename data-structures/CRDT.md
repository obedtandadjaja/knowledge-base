# CRDT

CRDT (Conflict-free Replicated Data Type) is a data structure where the replicas can be updated independently and concurrently without coordination between the replicas, and where it is always mathematically possible to resolve inconsistencies that might come up.

Redis, Riak, and Cosmos DB have CRDT data types.

## Why is it significant?

Concurrent updates to multiple replicas of the same data without coordination can result in inconsistencies between replicas. Restoring consistency and data integrity is hard and most likely data will be partially/entirely dropped.

## Types of CRDTs

There are 2 approaches, both provide strong eventual consistency.

### Operation-based CRDTs

Also called commutative replicated data types (CmRDTs). CmRDT replicas propagate state by transmitting only the update operation. The operations are commutative (change in order does not change the result), however they are not necessarily idempotent. The communications infrastructure must therefore ensure that all operations on a replica are delivered to other replicas, without duplication, but in any order.

### State-based CRDTs

Also called convergent replicated data types (CvRDTs). Sends their full local state to other replicas, where the states are merged by a function which must be commutative, associative, and idempotent.

- Merge function provides a join fucntion for any pair of replica states, so the set of all states forms a semilattice.
- Update function must monotonically increase the internal state.

### Comparison

Although CmRDTs place more requirements on the protocol for transmiting operations between replicas, they use less bandwidth than CvRDTs when the number of transactions is small in comparison to the size of internal state.

Gossip protocols can be used to propagate CvRDT state to other replicas while reducing network use and handling topology changes.

## Example CRDTs

### G-Counter (Grow-only Counter)

```
payload integer[n] P
    initial [0,0,...,0]
update increment()
    let g = myId()
    P[g] := P[g] + 1
query value() : integer v
    let v = Σi P[i]
compare (X, Y) : boolean b
    let b = (∀i ∈ [0, n - 1] : X.P[i] ≤ Y.P[i])
merge (X, Y) : payload Z
    let ∀i ∈ [0, n - 1] : Z.P[i] = max(X.P[i], Y.P[i])
```

This CvRDT implements a counter for a cluster of n nodes. Each node in the cluster is assigned an ID from 0 to n - 1, which is retrieved with a call to myId(). Thus each node is assigned its own slot in the array P, which it increments locally. 

Updates are propagated in the background, and merged by taking the max() of every element in P. The compare function is included to illustrate a partial order on the states. The merge function is commutative, associative, and idempotent. 

The update function monotonically increases the internal state according to the compare function. 

This is thus a correctly-defined CvRDT and will provide strong eventual consistency. The CmRDT equivalent broadcasts increment operations as they are received.

### PN-Counter (Positive-Negative Counter)

```
payload integer[n] P, integer[n] N
    initial [0,0,...,0], [0,0,...,0]
update increment()
    let g = myId()
    P[g] := P[g] + 1
update decrement()
    let g = myId()
    N[g] := N[g] + 1
query value() : integer v
    let v = Σi P[i] - Σi N[i]
compare (X, Y) : boolean b
    let b = (∀i ∈ [0, n - 1] : X.P[i] ≤ Y.P[i] ∧ ∀i ∈ [0, n - 1] : X.N[i] ≤ Y.N[i])
merge (X, Y) : payload Z
    let ∀i ∈ [0, n - 1] : Z.P[i] = max(X.P[i], Y.P[i])
    let ∀i ∈ [0, n - 1] : Z.N[i] = max(X.N[i], Y.N[i])
```

A common strategy in CRDT development is to combine multiple CRDTs to make a more complex CRDT. In this case, two G-Counters are combined to create a data type supporting both increment and decrement operations. The "P" G-Counter counts increments; and the "N" G-Counter counts decrements. The value of the PN-Counter is the value of the P counter minus the value of the N counter. Merge is handled by letting the merged P counter be the merge of the two P G-Counters, and similarly for N counters. Note that the CRDT's internal state must increase monotonically, even though its external state as exposed through query can return to previous values
