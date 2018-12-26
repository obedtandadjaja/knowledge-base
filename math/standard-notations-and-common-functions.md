# Standard Notations and Common Functions

## Table of Contents
1. [Monotonicity](#monotonicity)
2. [Floors and Ceilings](#floors_and_ceilings)
3. [Modular Arithmetic](#modular_arithmetic)
4. [Exponentials](#exponentials)
5. [Logarithms](#logarithms)
6. [Factorials](#factorials)
7. [Functional Iteration](#functional_iteration)

<a name='monotonicity'></a>
## Monotonicity
A function f(n) is **monotonically increasing** if `m <= n` implies `f(m) <= f(n)`.

Similarly, it is **monotonically decreasing** if `m <= n` implies `f(m) >= f(n)`.

A function f(n) is **strictly increasing** if `m < n` implies `f(m) < f(n)`.

Similarly, it is **strictly decreasing** if `m < n` implies `f(m) > f(n)`

<a name='floors_and_ceilings'></a>
## Floors and Ceilings
Denoted by:

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20ceil%3A%20%5Cleft%20%5Clceil%20n%20%5Cright%20%5Crceil%20%5C%5C%20floor%3A%20%5Cleft%20%5Clfloor%20n%20%5Cright%20%5Crfloor%20%5Cend%7Bmatrix%7D)

Ceil is the greatest integer less than or equal to x, and floor is the least integer less than or equal to x.

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20x%20-%201%20%3C%20%5Cleft%20%5Clfloor%20x%20%5Cright%20%5Crfloor%20%5Cleq%20x%20%5Cleq%20%5Cleft%20%5Clceil%20x%20%5Cright%20%5Crceil%20%3C%20x%20&plus;%201%20%5Cend%7Bmatrix%7D)

For any integer n,

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Cleft%20%5Clceil%20n/2%20%5Cright%20%5Crceil%20&plus;%20%5Cleft%20%5Clfloor%20n/2%20%5Cright%20%5Crfloor%20%3D%20n%20%5Cend%7Bmatrix%7D)

and for any real number `x >= 0` and integers `a, b > 0`,

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Cleft%20%5Clceil%20%5Cfrac%7B%5Cleft%20%5Clceil%20x/a%20%5Cright%20%5Crceil%7D%7Bb%7D%20%5Cright%20%5Crceil%20%3D%20%5Cleft%20%5B%20%5Cfrac%7Bx%7D%7Bab%7D%20%5Cright%20%5D%2C%20%5C%5C%20%5C%5C%20%5Cleft%20%5Clfloor%20%5Cfrac%7B%5Cleft%20%5Clfloor%20x/a%20%5Cright%20%5Crfloor%7D%7Bb%7D%20%5Cright%20%5Crfloor%20%3D%20%5Cleft%20%5Clfloor%20%5Cfrac%7Bx%7D%7Bab%7D%20%5Cright%20%5Crfloor%2C%20%5C%5C%20%5C%5C%20%5Cleft%20%5Clceil%20%5Cfrac%7Ba%7D%7Bb%7D%20%5Cright%20%5Crceil%20%5Cleq%20%5Cfrac%7Ba&plus;%28b-1%29%7D%7Bb%7D%2C%20%5C%5C%20%5C%5C%20%5Cleft%20%5Clfloor%20%5Cfrac%7Ba%7D%7Bb%7D%20%5Cright%20%5Crfloor%20%5Cleq%20%5Cfrac%7Ba-%28b-1%29%7D%7Bb%7D%20%5Cend%7Bmatrix%7D)

The floor function is monotonically increasing, as is the ceiling function.

<a name='modular_arithmetic'></a>
## Modular arithmetic
For any integer a and any positive integer n, the value a mod n is the **remainder** (or residue) of the quotient `a/n`.

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20a%5C%3Bmod%5C%3Bn%20%3D%20a%20-%20n%20%5Cleft%20%5Clfloor%20a/n%20%5Cright%20%5Crfloor%20%5Cend%7Bmatrix%7D)

It follows that

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%200%20%5Cleq%20a%5C%3Bmod%5C%3Bn%20%3C%20n%20%5Cend%7Bmatrix%7D)

## Exponentials
For all real `a > 0, m, and n`, we have the following identities:

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20a%5E0%20%3D%201%2C%20%5C%5C%20%5C%5C%20a%5E1%20%3D%20a%2C%20%5C%5C%20%5C%5C%20a%5E%7B-1%7D%20%3D%201/a%2C%20%5C%5C%20%5C%5C%20%28a%5Em%29%5En%20%3D%20a%5E%7Bmn%7D%2C%20%5C%5C%20%5C%5C%20%28a%5Em%29%5En%20%3D%20%28a%5En%29%5Em%20%5C%5C%20%5C%5C%20a%5Ema%5En%20%3D%20a%5E%7B%28m&plus;n%29%7D%20%5Cend%7Bmatrix%7D)

Any exponential function with a base strictly greater than 1 grows faster than any polynomial function.

Using e to denote 2.71828..., the base of the natural logarithm function, we have for all real x,

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20e%5Ex%20%3D%201%20&plus;%20x%20&plus;%20%5Cfrac%7Bx%5E2%7D%7B2%21%7D%20&plus;%20%5Cfrac%7Bx%5E3%7D%7B3%21%7D%20&plus;%20%5Ccdots%20%3D%20%5Csum_%7Bi%20%3D%200%7D%5E%7B%5Cinfty%7D%20%5Cfrac%7Bx%5Ei%7D%7Bi%21%7D%20%5Cend%7Bmatrix%7D)

For all real x, we have the inequality

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20e%5Ex%20%5Cgeq%201%20&plus;%20x%20%5Cend%7Bmatrix%7D)

where the equality holds only when `x = 0`. When `|x| <= 1`, we have the approximation

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%201%20&plus;%20x%20%5Cleq%20e%5Ex%20%5Cleq%201%20&plus;%20x%20&plus;%20x%5E2%20%5Cend%7Bmatrix%7D)

<a name='logarithms'></a>
## Logarithms
Notations:

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20log%5C%3Bn%20%3D%20log_2%20%5C%3Bn%5Cquad%28binary%20%5C%3B%20logarithm%29%2C%20%5C%5C%20%5C%5C%20ln%5C%3B%20n%20%3D%20log_e%20%5C%3Bn%5Cquad%28natural%20%5C%3B%20logarithm%29%2C%20%5C%5C%20%5C%5C%20lg%5Ek%5C%3Bn%20%3D%20%28lg%5C%3Bn%29%5Ek%20%5Cquad%28exponentiation%29%2C%20%5C%5C%20%5C%5C%20lg%20%5C%3Blg%5C%3Bn%20%3D%20lg%28lg%5C%3B%20n%29%20%5Cquad%28composition%29.%20%5Cend%7Bmatrix%7D)

For all real `a > 0, b > 0, c > 0, and n`:

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20a%20%3D%20b%5E%7Blog_b%5C%3Ba%7D%2C%20%5C%5C%20%5C%5C%20log_c%5C%3B%28ab%29%20%3D%20log_c%5C%3Ba%20&plus;%20log_x%5C%3Bb%2C%20%5C%5C%20%5C%5C%20log_b%5C%3Ba%5En%20%3D%20n%5C%3Blog_b%5C%3Ba%2C%20%5C%5C%20%5C%5C%20log_b%5C%3Ba%20%3D%20%5Cfrac%7Blog_c%5C%3Ba%7D%7Blog_c%5C%3Bb%7D%2C%20%5C%5C%20%5C%5C%20log_b%5C%3B%281/a%29%20%3D%20-log_b%5C%3Ba%2C%20%5C%5C%20%5C%5C%20log_b%5C%3Ba%20%3D%20%5Cfrac%7B1%7D%7Blog_a%5C%3Bb%7D%2C%20%5C%5C%20%5C%5C%20a%5E%7Blog_b%5C%3Bc%7D%20%3D%20c%5E%7Blog_b%5C%3Ba%7D%20%5Cend%7Bmatrix%7D)

where, in each equation above, logarithm bases are not 1.

For `x > -1`:

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Cfrac%7Bx%7D%7B1&plus;x%7D%20%5Cleq%20ln%281&plus;x%29%20%5Cleq%20x%20%5Cend%7Bmatrix%7D)

<a name='factorials'></a>
## Factorials
![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20n%21%20%3D%20%5Cbegin%7BBmatrix%7D%201%20%5Cqquad%5Cqquad%5Cqquad%5C%3B%20if%5C%3Bn%20%3D%200%20%5C%5C%20n%5Ccdot%28n-1%29%21%20%5Cqquad%20if%5C%3Bn%20%3E%200%20%5Cend%7BBmatrix%7D%20%5Cend%7Bmatrix%7D)

Thus, `n! = 1 . 2 . 3 . ... . n`

<a name='functional_iteration'></a>
## Functional iteration
We use the notation `f^(i)(n)` to denote the function `f(n)` iteratively applied i times to an initial value of n.

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20n%21%20%3D%20%5Cbegin%7BBmatrix%7D%20n%20%5Cqquad%5Cqquad%5Cqquad%5C%3B%5C%3B%20if%5C%3Bn%20%3D%200%2C%20%5C%5C%20f%28f%5E%7Bi-1%7D%28n%29%29%29%20%5Cqquad%20if%5C%3Bn%20%3E%200%20%5Cend%7BBmatrix%7D%20%5Cend%7Bmatrix%7D)

For example, if `f(n) = 2n`, then `f^(i)(n) = 2^i n`
