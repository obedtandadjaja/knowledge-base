**There are 2 types of sorting algorithms: comparison sorts and otherwise. Comparison sorts share a property: the sorted order they determine is based only on comparisons between the input elements. Comparison sorts run on O(n log n) at best, such as merge, heap and quick sorts. However, other sorts like counting, radix, and bucket sorts can run in linear time; they use operations other than comparisons to determine the sorted order**

| Algorithm      	| Worst-case running time 	| Average-case/expected running time 	|
|----------------	|-------------------------	|------------------------------------	|
| [Insertion Sort](#insertion_sort) 	| O(n^2)                  	| O(n^2)                             	|
| [Merge Sort](#merge_sort)     	| O(n log n)              	| O(n log n)                         	|
| [Heapsort](#heap_sort)       	| O(n log n)              	| -                                  	|
| [Quick Sort](#quick_sort)      	| O(n^2)                  	| O(n log n) {expected}              	|
| [Counting sort](#counting_sort)  	| O(k + n)                	| O(k + n)                           	|
| [Radix sort](#radix_sort)     	| O(d(n + k))             	| O(d(n + k))                        	|
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

The running time of quicksort depends on whether the partitioning is balanced, which in turn depends on which elements are used for partitioning. 

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

In-place algorithm with fixed sampling:

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

In-place algorithm with random sampling (in order to obtain good performance over all inputs):
```python
randomizedQuickSort(A, start, end):
  if start < end:
    pivot_index = randomizedPartition(A, start, end)
    randomizedQuickSort(A, start, pivot_index - 1)
    randomizedQuickSort(A, pivot_index + 1, end)

randomizedPartition(A, start, end):
  pivot_index = random(start, end)
  swap(A, pivot_index, end - 1)
  return partition(A, start, end) # see above for partition logic
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
<a name='counting_sort'></a>
# Counting Sort
Assumes that each of the `n` input elements is an integer in the range 0 to `k`, for some integer `k`. Counting sort uses the actual values of the elements to index into an array. And use running sums to determine the index of where the value needs to go.

Good resource: https://www.youtube.com/watch?v=7zuGmKfUt7 

Time complexity: O(k + n)
Space complexity: O(k + n)

#### Pseudocode:
```python
countingSort(A, k):
  B = [0..A.length] a new array
  C = [0..k] a new array
  
  # count how many counts per index
  for i = 1 to A.length - 1:
    C[A[i]] += 1
  
  # calculate running sum
  for i = 1 to k
    C[i] = C[i] + C[i-1]
  
  # the running sum will determine the position of where the value need to go
  # decrement the count when finished so that the next iteration of the same value
  # will go to a lower index
  for i = A.length - 1 downto 0:
    B[C[A[i]]] = A[i]
    C[A[i]] -= 1
  
  return B
```

<a name='radix_sort'></a>
# Radix Sort
Idea behind radix sort is to sort by digits starting from the least significant digit to the most significant. 

Radix sort is very good at sorting dates by sorting each date in 3 categories: day, month, year. 

Time complexity: O(d(k + n))
Space complexity: O(d(k + n))

#### Pseudocode:
```python
radixSort(A, d):
  for i = d-1 down to 0:
    use a stable sort to sort array A on digit i
```
