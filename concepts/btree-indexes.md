# B+tree Indexes

B+tree indexes are important part of databases and are used frequently to speed up access to record(s). 

![](https://github.com/obedtandadjaja/knowledge-base/blob/master/pictures/B+tree.png?raw=true)

B+tree is in many ways similart to a binary search tree (i.e. left = val < currentVal, right > currentVal). But there are some very important differences:
- B+tree can have more than 1 key in a node. Since the branching factor of a B+tree can be very large, this allows it to be a lot shallower as compared to BST.
- B+trees have all the key values in their leaf nodes. All leaf nodes of a B+tree are at the same height, which implies that **every index lookup will take same number of B+tree lookups to find a value**.
- Within a B+tree all leaf nodes are linked together (siblings). And since the values at the leaf nodes are sorted, so range lookups are very efficient.

## Why use B+tree?

As DB size grows, it is not possible to keep the tree indexing in memory so we need to do a fetch to the disk. Since B+tree is very efficient in keeping branching factor low, it makes it possible to store billion key values (with pointers to billion rows) at a height of 3, 4, or 5 so that every key lookup is going to take only 3, 4, or 5 disk accesses.

## How B+tree is structured?

The size of a node in a B+tree is chosed according to the page size. Why? Because whenever data is accessed on disk, instead of reading a few bits, a whole page of data is read, because that is much cheaper.

Consider InnoDB whose page size is 16KB and suppose we have an index on a integer column of size 4bytes, so a node can contain at most `16 * 1024 / 4 = 4096 keys`, and a node can have at most 4097 children.
So for a B+tree of height 1, the root node has 4096 keys and the nodes at height 1 (the leaf nodes) have `4096 * 4097 = 16781312` key values.
This goes to show the effectiveness of a B+tree index, more than 16 million key values can be stored in a B+tree of height 1 and every key value can be accessed in exactly 2 lookups.

## How important is the size of the index values?

Very important. Consider the following:
- The longer the index, the less number of values that can fit in a node --> higher branching factor
  - Higher branching factor == more disk accesses
    - More disk accesses == less performance
