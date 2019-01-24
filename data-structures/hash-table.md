# Hash Table
A hash table is an effective data structure to implement dictionaries.

Search: O(1) average, O(n) worst-case - depends on the hashing algorithm

## Implementation
Figuring out the slot for a given key: `hash(key) % array.size`

A hash function must be deterministic in that a given input `k` should always produce the same output.

### Collision resolution

#### Chaining
Have each slot of the hash table to be linkedlists. 

```python
chainedHashInsert(HT, x):
  insert x at the head of list HT[hash(x.key)]
  
chainedHashSearch(HT, key):
  search for an element with key in list HT[hash(key)]
  
chainedHashDelete(HT, x):
  delete x from the list HT[hash(x.key)]
```

Insertion: O(1)
Deletion: O(n) - O(1) average case
Search: O(n) - O(1) average case

#### Linear Probing
When we come to a collision, occupy the next available slot after that index.

```python
linearProbingHashInsert(HT, x):
  index = hash(x.key)
  while HT[index] != nil:
    index = (index + 1) % HT.length
  HT[index] = x

linearProbingHashSearch(HT, x):
  index = hash(x.key)
  while HT[index].key != x.key:
    index = (index + 1) % HT.length
  return HT[index]
  
linearProbingHashDelete(HT, x):
  index = hash(x.key)
  while HT[index].key != x.key:
    index = (index + 1) % HT.length
  HT[index] = nil
```

This method suffers from **primary clustering** where long runs of occupied slots build up, increasing the average search time.

#### Quadratic Probing
When we come to a collusion, run the following formula to get the next hash function:

```python
h(k, attempts_count) = (h'(k) + constant_1 * attempts_count + constant_2 * attempts_count)

h'(k) = value of previous hash
```

This method works much better than linear probing, but it suffers from a problem called **secondary clustering**. If two keys have the same initial probe position, then their probe sequences are the same.
