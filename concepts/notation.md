## Big-O
O(n) denotes the asymptotic upper bound (worst-case running time of the algorithm on every input) of a function.

`O(g(n)) = { f(n): there exist positive constants c and n0 such that 0 <= f(n) <= cg(n) for all n >= n0 }`

## Big-Omega
Ω(n) denotes the asymptotic lower bound (best-case running time of the algorithm on every input) of a function.

`Ω(g(n)) = { f(n): there exist positive constants c and n0 such that 0 <= cg(n) <= f(n) for all n >= n0 }`

No matter what particular input of size n is chosen for each value of n, the running time on that input is at least a constant times g(n), for sufficiently large n.

## Recap
Running time of insertion sort therefore belongs to both **Ω(n)** and **O(n^2)**, since it falls anywhere between a linear function of n and a quadratic function of n.

These bounds should be asymptotically as tight as possible. Even though it is not contradictory to say that the worst-case running time of insertion sort is Ω(n^2), since there exists an input that causes the algorithm to take Ω(n^2) time.

Using asymptotic notation in this manner can help eliminate inessential detail and clutter in an equation: they are all understood to be included in the anonymous function denoted by the term O(g(n)).

## Math

In general, when asymptotic notation appears in a formula, we interpret it as stading for some anonymous function that we do not care to name. For example, the formula **2n^2 + 3n + 1 = 2n^2 + O(n)** means that **2n^2 + 3n + 1 = 2n^2 + f(n)**, where f(n) is some function in the set O(n). 

No matter how the anonymous functions are chosen on the left of the equal sign, there is a way to choose the anonymous functions on the right of the equal sign to make the equation valid.

2n^2 + O(n) = O(n^2)

In other words, the right-hand side of an equation provides a coarser level of detail than the left-hand side.

## Little-O
o(n) denotes an uper bound that is not asymptotically tight.

`o(g(n)) = { f(n): for any positive constant c > 0, there exists a constant n0 > 0 such that 0 <= f(n) < cg(n) for all n >= n0 }`

For example, 2n = o(n^2), but 2n^2 != o(n^2)

The definitions of O-notation and o-notation are similar. The main difference is that in f(n) = O(g(n)), the bound 0 <= f(n) <= cg(n) holds for some constant c > 0, but in f(n) = o(g(n)), the bound 0 <= f(n) < cg(n) holds for all constants c > 0. 

## Little-Omega
ω(n) denotes a lower bound that is not asymptotically tight.

`ω(g(n)) = { f(n): for any positive constant c > 0, there exists a constant n0 > 0 such that 0 <= cg(n) < f(n) for all n >= n0 }`

For example, n^2/2 = ω(n), but n^2/2 != ω(n^2)
