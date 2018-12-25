## Divide and conquer

Solving a problem recursively, applying 3 steps at each level of the recursion:
1. **Divide** the problem into a number of subproblems that are smaller instances of the same problem.
2. **Conquer** the subproblems by solving them recursively. If the subproblem sizes are small enough, however, just solve the subproblems in a straightforward manner.
3. **Combine** the solutions to the subproblems into the solution for the original problem.

#### Recursive case
When the subproblems are large enough to solve recursively.

#### Base case
When the subproblems become small enough that we no longer recurse, and we just solve it directly.

### Recurrence
Recurrences go hand in hand with the divide-and-conquer paradigm, because they give us a natural way to characterize the running times of divide-and-conquer algorithms. A **recurrence** is an equation or inequality that describes a function in terms of its value on smaller inputs. For example:

```
T(n) = T(2n/3) + T(n/3) + O(n)
```

When we state and solve recurrences, we often omit floors, ceilings, and boundary conditions. We forge ahead without these details and later determine whether or not they matter. They usually do not, but you should know when they do.
