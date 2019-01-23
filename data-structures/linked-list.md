#Linked List
Linked list is a data structure in which the objects are arranged in a linear order. Unlike an array, however, the linear order is not determined by the array indices, rather the order is determined by a pointer in each object. 

Each element of a **doubly linked list** is an object with 2 pointer attributes: `next` and `prev`.

## Implementation

```python
search(L, x): # O(n)
  curr = L.head
  while curr != nil and curr.key != x:
    curr = curr.next
  return curr
  
insert(L, x): # O(1)
  new_node = new Node(x)
  new_node.next = L.head
  
  if L.head != nil:
    L.head.prev = new_node
  
  L.head = new_node
  
delete(L, node):
  if node.prev != nil:
    node.prev.next = node.next
  else:
    L.head = node.next
  
  if node.next != nil:
    node.next.prev = node.prev
```

Java implementation: https://github.com/obedtandadjaja/interview_misc/blob/master/interview201710/DataStructures/LinkedList.java
