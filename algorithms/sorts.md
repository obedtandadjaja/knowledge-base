# Table of contents
1. [Insertion Sort](#insertion_sort)
2. [Merge Sort](#merge_sort)
3. [Quick Sort](#quick_sort)

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
