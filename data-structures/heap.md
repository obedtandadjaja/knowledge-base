# Heap
Heap data structure is an array object that we can view as a nearly complete binary tree. It always has either the max (max heap) or min (min heap) value at the root (index 0) with each children's values either bigger (min heap) or smaller (max heap) than their parents.

Heap typically have 2 attributes:

1. length: The length of the array
2. heap-size: The number of elements currently in the array

Insertion: 
Deletion:

#### Heap property
Max-heap: `A[parent(i)] >= A[i]`

Min-heap: `A[parent(i)] <= A[i]`

#### Navigation Formula
Let `i` be the index of the current element.

Go to parent: `i / 2`

Go to left-child: `2i`

Go to right-child: `2i + 1`

Height: log_2 (size)

#### Basic Procedures
max-heapify: procedure to maintain the heap property - O(log n)

```python
max-heapify(A, i):
  l = 2 * i
  r = 2 * i + 1
  if l <= A.heap-size and A[l] > A[i]
    swap(A[l], A[i])
    max-heapify(A, l)
  else if r <= A.heap-size and A[r] > A[i]
    swap(A[r], A[i])
    max-heapify(A, r)
```

build-max-heap: produces a max-heap from an unordered array - O(n)

```python
build-max-heap(A):
  A.heap-size = A.length
  for i = A.length/2 downto 1:
    max-heapify(A, i)
```

Elements in the subarray A[(n/2 + 1) ... n] are all leaves of the tree
#### Implementations
Heapsort uses max-heap.

```python
heap-sort(A):
  build-max-heap(A)
  for i = A.length downto 1:
    swap(A[i], A[0])
    A.heap-size -= 1
    build-max-heap(A, 1)
```

Min-heaps commonly implement priority queues.

```python
heap-maximum(A): # get the max
  return A[0]
  
heap-extract-max(A): # remove max
  if A.heap-size < 1:
    error "heap is empty"
  max = A[0]
  
  A[0] = A[A.heap-size]
  A.heap-size -= 1
  max-heapify(A, 1)
  
  return max
  
heap-increase-key(A, i, key): # insert key at an index
  if key < A[i]:
    error "new key is smaller than current key"
  
  A[i] = key
  while i > 0 and A[i/2] < A[i]:
    swap(A[i], A[i/2])
    i = i/2
    
max-heap-insert(A, key): # insert key to heap
  A.heap-size += 1
  A[A.heap-size] = -infinity
  heap-increase-key(A, A.heap-size, key)
```
