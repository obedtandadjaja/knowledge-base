| Algorithm      	| Worst-case running time 	| Average-case/expected running time 	|
|----------------	|-------------------------	|------------------------------------	|
| [Insertion Sort](#insertion_sort) 	| O(n^2)                  	| O(n^2)                             	|
| [Merge Sort](#merge_sort)     	| O(n log n)              	| O(n log n)                         	|
| [Heapsort](#heap_sort)       	| O(n log n)              	| -                                  	|
| [Quick Sort](#quick_sort)      	| O(n^2)                  	| O(n log n) {expected}              	|
| Counting sort  	| O(k + n)                	| O(k + n)                           	|
| Radix sort     	| O(d(n + k))             	| O(d(n + k))                        	|
| Bucket         	| O(n^2)                  	| O(n) {average-case}                	|

<a name='insertion_sort'></a>
# Insertion Sort
Idea behind this sort is basically keeping a subarray of sorted values, and inserting new values to this subarray until everything is sorted.

Time complexity: O(n^2)
Space complexity: O(1)

#### Pseudocode:
```python
for j = 2 to A.length
  key = A[j] # key to insert

  # inserting A[j] into the sorted sequence A[1 .. j-1]
  i = j - 1
  
  while i > 0 and A[i] > key # looping through the sorted subarray
    A[i + 1] = A[i] # shifting
    i = i -1

  A[i + 1] = key
```

<a name='merge_sort'></a>
# Merge Sort
A divide-and-conquer approach where we try to break down a problem to smaller problem (i.e. comparing small sorted arrays).

**Divide**: Divide the n-element sequence to be sorted into 2 subsequences of n/2 elements each

**Conquer**: Sort the 2 subsequences recursively using merge sort

**Combine**: Merge the 2 sorted subsequences to produces the sorted answer

Time complexity: O(n log n)
Space complexity: O(n) - there exist an in-place merge sort but it hurts performance due to swaps

#### Pseudocode:
```python
mergeSort(A, p, r):
  if p < r:
    q = (p + r) / 2
    mergeSort(A, p, q)
    mergeSort(A, q + 1, r)
    merge(A, p, q, r)
    
merge(A, p, q, r):
  arr1 = A[p .. q]
  arr2 = A[q+1 .. r]
  
  arr1_idx = 0
  arr2_idx = 0
  A_idx = 0
  
  while A_idx < r:
    if arr1_idx >= q:
      A[A_idx] = arr2[arr2_idx]
      arr2_idx += 1
    else if arr2_idx >= r:
      A[A_idx] = arr1[arr1_idx]
      arr1_idx += 1
    else if arr1[arr1_idx] < arr2[arr2_idx]:
      A[A_idx] = arr1[arr1_idx]
      arr1_idx += 1
    else:
      A[A_idx] = arr2[arr2_idx]
      arr2_idx += 1

    A_idx += 1
```

<a name='quick_sort'></a>
# Quick Sort
Idea behind this sort is basically choosing a pivot value and categorizing values into 2 sections, those that are higher and lower than the pivot value.

Quicksort is often the best practical choice for sorting becuase it is remarkably efficient on the average. It also has the benefit of sorting in place and it works well even in virtual-memory environments.

**Divide**: Choose a pivot value.

**Conquer**: Categorize the remaining values to array of higher or lower than the pivot value.

**Combine**: Merge the lower array, pivot value, and higher array together.

Time complexity: typically O(n log n), with worst case up to O(n^2)
Space complexity: O(n) - there exist an in-place quick sort but it hurts performance due to swaps

#### Pseudocode:
```python
quickSort(A):
  lower = []
  higher = []
  pivot = A[randomInt() * len(A)]
  
  for i = 0 to A.length:
    if A[i] < pivot:
      lower.append(A[i])
    else:
      higher.append(A[i])
  
  lower = quickSort(lower)
  higher = quickSort(higher)
  
  return lower.append(pivot).append(higher)
```

In-place algorithm:

```python
quickSort(A, start, end):
  if start < end:
    pivot_index = partition(A, start, end)
    quickSort(A, start, pivot_index - 1)
    quickSort(A, pivot_index + 1, end)

# 1. set the pivot to be the last element in the subarray
# 2. set a partition index to separate the "lower" and "higher" arrays
# 3. loop through from start to end-2, and have the current index be i
# 4. if the current value is bigger than pivot then increment the parition index and swap
# 5. lastly, swap the pivot with the partition index so that the pivot is in the middle
partition(A, start, end):
  pivot = A[end-1]
  partition_index = start - 1
  
  for i = start to end-2: # we choose end-2 since the last index is the pivot
    if A[i] <= pivot:
      partition_index += 1
      swap(A, partition_index, i)
  
  swap(A, parition_index + 1, end - 1)
  
  return partition_index + 1
```

<a name='heap_sort'></a>
# Heap Sort
Uses a heap to sort the elements and sorts everything in-place. Idea is to:

1. Build a max heap from the array
2. Keep swapping the root (max) with the last element in the array (min)
3. Decrement size of the heap so that the last element will not get included
4. Maintain the heap property

Time complexity: O(n log n)
Space complexity: O(1)

#### Recap Heap
Heap is an array object that we can view as nearly complete binary tree. The tree is completely filled on all levels except possibly the lowest, which is filled from the left up to a point.

Insertion: Have the new element to be at the end of the array and iterate up to the root (parent: n/2)
Deletion: Have the last element to replace the deleted node and iterate downwards (2n or 2n+1) to maintain the heap property.

#### Pseudocode:
```python
heapSort(A):
  buildMaxHeap(A)
  for i = A.length downto 2
    swap(A[0] with A[i])
    A.heap-size -= 1
    maintainMaxHeap(A)
```
