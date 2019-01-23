# Queue
Queues are dynamic sets where the element deleted is the first one inserted, implementing a FIFO (First In First Out) policy.

Insert operation on a queue is called `ENQUEUE` and the delete operation is called `DEQUEUE`. The queue has a head and a tail, when an element is enqueued, it takes its place at the tail of the queue. The element dequeued is always the one at the head of the queue.

## Implementation
We can use a simple array with two variables `head` and `tail` to implement a queue.

```python
enqueue(Q, x):
  Q.tail = (Q.tail + 1) % Q.length
  Q[Q.tail] = x

dequeue(Q):
  x = Q[Q.head]
  Q.head = (Q.head + 1) % Q.length
  return x
```

We can also implement a queue with doubly linked list so that we do not have to grow the array once the queue exceeds the size of the array.

We can also implement a queue using 2 stacks.
