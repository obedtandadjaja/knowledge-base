# Probabilistic Analysis
Often used to analyze the running time of an algorithm.

Must use knowledge of, or make assumptions about, the distribution of the inputs. Then we analyze our algorithm, computing an average-case running time, where we take the average over the distribution of the possible inputs. When reporting such a running time, we will refer to it as the **average-case running time**.

For problems where we cannot describe a reasonable input distribution, we cannot use probabilistic analysis.

If we were to rank the inputs from 1 to n, and that the inputs come in random order; this is equivalent to saying that this list of ranks is equally likely to be any one of the n! permutations of the numbers 1 through n. Alternatively, we say that the ranks form a **uniform random permutation**; that is, each of the possible n! permutation appears with equal probability.

# Randomized Algorithms
Generally, we can call an algorithm randomized if its behavior is determined not only by its input but also by values produced by a **random-number-generator**. 

**pseudo-number-generator**: a deterministic algorithm returning numbers that "look" statistically random.

We distinguish these algorithms from those in which the input is random by referring to the running time of randomized algorithm as an **expected running time**. In general, we discuss the average-case running time when the probability distribution is over the inputs to the algorithm, and we discuss the expected running time when the algorithm itself makes random choices.

#### Indicator random variables
Provides a convenient method for converting between probabilities and expectations.

Denoted by:
```
I{A} = { 1 if A occurs,
         0 if A does not occur. }
```

E.g. flipping coins:

Sample space: ```S = {H, T}```

Probabilities: ```Pr{H} = Pr{T} = 1/2```

We can then define an indicateor random variable X_H, associated with the coin coming up heads, which is the event H. This variable counts the number of heads obtained in this flip.

```
X_H = I{H}
    = { 1 if H occurs,
        0 if T occurs. }
```

The expected number of heads obtained in one flip of coin is simply the expected value of our indicator variable X_H:

```
E[X_H] = E[I{H}]
       = 1 . Pr{H} + 0 . Pr{T}
       = 1 . (1/2) + 0 . (1/2)
       = 1/2
```

Let `X_i = I{the i-th flip results in the event H}`

Let X be the random variable denoting the total number of heads in the n coin flips.

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Balign*%7DE%5BX%5D%20%26%3D%20E%5B%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%20X_i%5D%20%5C%5C%20%26%3D%20%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%20E%5BX_i%5D%20%5C%5C%20%26%3D%20%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%201/2%20%5C%5C%20%26%3D%20n/2%20%5Cend%7Balign*%7D)  
