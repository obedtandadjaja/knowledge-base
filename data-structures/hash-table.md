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
h(k, i) = (h'(k) + constant_1 * i + constant_2 * i)

h'(k) = value of previous hash
where k = key
where i = number of attempts
```

This method works much better than linear probing, but it suffers from a problem called **secondary clustering**. If two keys have the same initial probe position, then their probe sequences are the same.

#### Double Hashing
```python
h(k, i) = (h_1(k) + i*h_2(k)) mod m

where k = key
where i = number of attempts
where h_1 and h_2 are auxillary hash functions
```

Successive probe positions are offset from previous positions by the amount `h_2(k) mod m`. Hence, unlike the case of linear or quadratic probing, the probe sequence here depends in 2 ways upon the key `k`, since the initial probe position, the offset, or both, may vary.

## What makes a good hash function?
Goal: each key is equally likely to hash to any of the slots, independently of where any other key has hashed to.

However, this is hard to do since we rarely know the probability distribution of the keys. 

### The Division Method
Map a key `k` into one of `m` slots by taking the remainder of `k` divided by `m`.

```python
h(k) = k mod m
```

When using the division method, we usually avoid certain values of `m`. For ecample `m` should not be a power of 2, since if `m = 2^p`, then `h(k)` is just the `p` lowest-order bits of `k`.

A prime not too close to an exact power of 2 is often a good choice for `m`.

### The Multiplication Method
```python
h(k) = floor(m(kA mod 1))

where A is a constant in the range of 0 < A < 1

"kA mod 1" means the fractional part of kA, that is, kA - floor(kA)
```

An advantage of the multiplication method is that the value of `m` is not critical. We typically choose it to be a power of 2 (`m = 2^p` for some integer `p`), since we can easily implement the function on most computers as follows.

Although the method works with any value of the constant `A`, it works better with some values than other. Knuth suggests that

```
A ~= (sqrt(5)-1)/2 = 0.6180339887
```

### Universal Hashing
Choose a hash function randomly in a way that is independent of the keys that are actually going to be stored. Universal hashing can yield provably good performance on average, no matter which keys are chosen.

At the beginning of execution we select the hash function at random from a carefully designed class of functions. As in the case of quicksort, randomization guarantees that no single input will always evoke worst-case behavior. Because we randomly select the hash function, the algorithm can behave differently on each execution, even for the same input, guaranteeing good average-case performance for any input.

Universal hashing algorithms do not use randomness when calculating a hash for a key. Random numbers are only used during the initialization of the hash table to choose a hash function from a family of hash functions. This prevents an adversary with access to the details of the hash function from devising a worst case set of keys.

In other words, during the lifetime of the hash table, the bucket for a given key is consistent. However, a different instance (such as next time the program runs) may place that same key in a different bucket.

The collection of hash functions are said to be universal if for each pair of distinct keys `k` and `l`, the number of hash functions for which `h(k) = h(l)` is at most `|H|/m` where `H` is the finite collection of hash functions.

A typical hash function look like:

```python
h_ab(k) = ((ak + b) mod p) mod m

where a is a constant from 0 to p-1
where b is a constant from 1 to p-1
where p is a large prime number
where m is the number of slots in the hash table
```

## Perfect Hashing
Perfect hashing is useful when the set of keys are **static**L once the keys are stored in the table, the set of keys never changes. 

We call a hashing technique **perfect hashing** if O(1) memory accesses are required to perform a search in the worst case.

To create a perfect hashing scheme, we use 2 levels of hashing, with universal hashing at each level.

The first level is essentially the same as for hashing with chaining: we hash `n` keys into `m` slots using a hash function `h` carefully selected from a family of universal functions.

Instead of making a linked list of the keys hashing to slot `j`, we use a small **secondary hash table** with an associated hash function `h_j`. By choosing the hash functions `h_j` carefully, we can guarantee that there are no collisions at the secondary level.

In order to guarantee there are no collisions at the secondary level, we will need to let the size of the secondary hash table be the square of the number of keys hashing to slot `j`. 
