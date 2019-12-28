# Distributed cache

Requirements:

- put(key, value) void
- get(key) value

Non-functional Requirements:

- Scalable
- Highly available
- Highly performant (< 100 ms)

## Solution

![](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/Screen%20Shot%202019-12-27%20at%204.05.19%20PM.png?raw=true)

### Eviction policy

LRU - Least Recently Used. We keep track of this using a double linked list. Our map will be in the form of Map<Key, Node> where Node is the doubly linked list node.

- get
  - does the key exist?
    - yes
      - delete in DLL
      - put node to head of DLL
    - no
      - return null
      
- put
  - does the key exist?
    - yes
      - delete in DLL
      - put node to head of DLL
    - no
      - is map full?
        - yes
          - delete tail in DLL
          - delete tail node in map
          - insert node to map
          - insert node to head of DLL
        - no
          - insert node to map
          - insert node to head of DLL

### Distribution of responsibility

Should the LRU cache be in a separate host or in the same host? 

Same host allows for both to scale at the same time, does not add extra complexity, and cheaper in the sense that you do not need to spin a whole new pod for it.

Separate host allows the cache to be separated and so it allows for the cache to be redundant to reduce bottleneck and allow more throughput.

### Distributing load

Use consistent hashing

- easy to add/remove nodes and reassign the load accordingly
- prevents hot spots by having virtual hosts

How to achieve this? Use a cache client. What should it do?

1. Knows all cache servers
2. All clients have the same list of cache servers
3. Stores list in sorted order (consistent hashing requirement) -> use TreeMap
4. Use binary serach to identify the server
5. If server is unavailable, treat it like a cache miss

### How to mutate and distribute cache server list?

Use ZooKeeper as configuration service

- checks periodically if servers are up (heartbeat check). If not then remove from the list of active cache servers
- if new server spawns, add to the list of active cache servers

### How to achieve high availability?

Leader-slave architecture

- many read replicas to prevent bottleneck and single point of failure
- leader gets elected if one dies
- propagates invalidation message to cache servers in different data centers

Zookeeper availability

- use leaderless architecture
- decide on a configuration list through quorum
