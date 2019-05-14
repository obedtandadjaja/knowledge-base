# Consistent Hashing

- Used to load balance non-uniformly distributed data (avoid hot spots)
- A hashing mechanism that is horizontally scalable
  - if nodes died or created the hashing function will still route requests to the same nodes
  
![picture](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/Ringpop-Hash-Ring.png?raw=true)
  
## How it works

Divide key space into buckets and let nodes in the cluster take ownership of key spaces.

- Imagine that an array of integers are placed on a ring such that the values are wrapped around
- Given a list of nodes, hash them to integers in the range
- Map a key to a node:
  - Hash the key to an integer
  - Move clockwise on the ring until we find the first node it encounters
- Handling hot spots:
  - Add "virtual replicas" - instead of mapping each node to a single point on the ring, map it to multiple points on the ring with different hash functions
  - As the number of virtual replicas increases, the keys will be more distributed evenly
  - Because the virtual replicas are pseudo-randomly distributed so it is not biased on a particular input key and thus limits hot spots
  - Handles the case where there is only 5 nodes to handle 1,000,000 keys (200,000 per node) where the majority of requests get hashed to 0 - 200,000. If we provide 10 virtual replicas per node, the keys will be more evenly distributed
  
resource: https://www.youtube.com/watch?v=zaRkONvyGr8
