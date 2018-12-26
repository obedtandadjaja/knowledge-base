Goal for algorithms here is to generate a uniform random permutation.

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

#### Proof
To see that the result is uniformly shuffled, observe that initially every element has `1/n` chance to land in place 0 and `n−1/n` to land in one of the remaining places 1,...,n−1. Therefore place 0 is uniformly shuffled. 

In the next step, every element has probability `(1/n−1).(n−1/n)` to land in place 1, because this happens when it is not placed at 0 (probabilty `n−1/n`) and it is selected to be at place 1 (probability `1/n−1`). We may proceed inductively: an element lands in place i with probability

```(n−1/n).(n−2/n−1).(n−3/n−2). ... .(n−i/n−i+1).(1/n−i)=1/n```

The first i factors arise for an element not being placed anywhere among places 0,…,i−1, and the last one for it being chosen to be placed at i.

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

#### Proof
let E_i be the event that element A[i] receives the i-th smallest priority. Then we wish to compute the probability that for all i, event E_i occurs.

We have that `Pr{E_1} = 1/n` because it is the probability that one priority chosen randomly out of a set of n is the smallest priority. Next we observe that `Pr{E_2 | E_1} = 1/(n-1)` because given that element A[1] has the smallest priority, each of the remaining n-1 elements has an equal chance of having the second smallest priority.

In general, for i = 2, 3, ..., n, we have that `Pr{E_i | E_i-1 n E_i-2 n ... n E_1 } = 1/(n - i + 1)`, since, given the elements A[1] through A[i-1] have the i-1 smallest priorities (in order), each of the remaining n-(i-1) elements has an equal chance of having the i-th smallest priority. Thus we have:

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Balign*%7DPr%5Cleft%20%5C%7B%20E_1%20%5Ccap%20E_2%20%5Ccap%20...%20%5Ccap%20E_%7Bn-1%7D%20%5Ccap%20E_%7Bn%7D%20%5Cright%20%5C%7D%20%26%3D%20%28%5Cfrac%7B1%7D%7Bn%7D%29%28%5Cfrac%7B1%7D%7Bn-1%7D%29...%28%5Cfrac%7B1%7D%7B2%7D%29%28%5Cfrac%7B1%7D%7B1%7D%29%20%5C%5C%20%26%3D%20%5Cfrac%7B1%7D%7Bn%21%7D%20%5C%5C%20%5Cend%7Balign*%7D)
