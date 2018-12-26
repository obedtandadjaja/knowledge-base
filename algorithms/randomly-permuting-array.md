# Table of Contents
1. [Randomize In-place](#randomize_in_place)
2. [Permute By Sorting](#permute_by_sorting)

<a name="randomize_in_place"></a>
## Randomize In-place
Time complexity: O(n)
Space complexity: O(1)

#### Pseudocode:
```python
randomizeInPlace(A):
  n = A.length
  for i = 1 to n
    swap A[i] with A[Random(i, n)]
```

In its i-th iteration , it chooses the element A[i] randomly from among elements A[i] through A[n]. Subsequent to the i-th iteration, A[i] is never altered.

<a name="permute_by_sorting"></a>
## Permute By Sorting
Idea is to create a priority array and have the original array to be sorted with the priority array as sort keys.

Time complexity: O(n log n)
Space complexity: O(n)

#### Pseudocode:
```python
permuteBySorting(A):
  n = A.length
  let P[1..n] be a new array
  for i = 1 to n
    P[i] = Random(1, n^3)
  sort A, using P as sort keys
```

Java implementation:
```java
public class PermuteBySorting {
    public static void main(String[] args) {
        class PrioritizedValue<T> implements Comparable<PrioritizedValue<T>> {
            final T value;
            final int priority;
            PrioritizedValue(T value, int priority) {
                this.value = value;
                this.priority = priority;
            }
            @Override public int compareTo(PrioritizedValue other) {
                return Integer.valueOf(this.priority).compareTo(other.priority);
            }           
        }
        int[] nums = { 1, 2, 3, 4 };
        int[] priorities = { 36, 3, 97, 19 };
        final int N = nums.length;
        List<PrioritizedValue<Integer>> list =
            new ArrayList<PrioritizedValue<Integer>>(N);
        for (int i = 0; i < N; i++) {
            list.add(new PrioritizedValue<Integer>(nums[i], priorities[i]));
        }
        Collections.sort(list);
        int[] permuted = new int[N];
        for (int i = 0; i < N; i++) {
            permuted[i] = list.get(i).value;
        }
        System.out.println(Arrays.toString(permuted));
        // prints "[2, 4, 1, 3]"
    }
}
```
