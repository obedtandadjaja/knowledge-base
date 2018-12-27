# Summations

## Notation
Given a sequence `a_1, a_2, ..., a_n` of numbers, where n is a nonnegative integer, we can write the finite sum `a_1 + a_2 + ... + a_n` as

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%20%3D%201%7D%5E%7Bn%7D%20a_k%20%5Cend%7Bmatrix%7D)

Given an infinite sequence of numbers, we can write the infinite sum as

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%20%3D%201%7D%5E%7B%5Cinfty%7D%20a_k%20%5Cend%7Bmatrix%7D)

which we interpret to mean

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Clim_%7Bn%20%5Cto%20%5Cinfty%7D%5Csum_%7Bk%20%3D%201%7D%5E%7B%5Cinfty%7D%20a_k%20%5Cend%7Bmatrix%7D)

## Linearity
For any real number c and any finite sequences a and b,

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D1%7D%5E%7Bn%7D%28ca_k%20&plus;%20b_k%29%20%3D%20c%5Csum_%7Bk%3D1%7D%5E%7Bn%7Da_k%20&plus;%20%5Csum_%7Bk%3D1%7D%5E%7Bn%7Db_k%20%5Cend%7Bmatrix%7D)

We can exploit the linearity property to manipulate summations incorporating asymptotic notation.

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D1%7D%5E%7Bn%7D%5CTheta%20%28f%28k%29%29%20%3D%20%5CTheta%28%5Csum_%7Bk%3D1%7D%5E%7Bn%7Df%28k%29%29%20%5Cend%7Bmatrix%7D)

In this equation, the O-notation on the left-hand side applies to the variable `k`, but on the right-hand side, it applines to `n`.

## Arithmetic series
The summation

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D1%7D%5E%7Bn%7Dk%20%3D%201%20&plus;%202%20&plus;%20%5Ccdots%20&plus;%20n%20%5Cend%7Bmatrix%7D)

is an **arithmetic series** and has the value

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D1%7D%5E%7Bn%7Dk%20%3D%20%5Cfrac%7Bn%28n&plus;1%29%7D%7B2%7D%20%3D%20%5CTheta%28n%5E2%29%20%5Cend%7Bmatrix%7D)

## Sums of squares and cubes
![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D1%7D%5E%7Bn%7Dk%5E2%20%3D%20%5Cfrac%7Bn%28n&plus;1%29%282n&plus;1%29%7D%7B6%7D%20%5C%5C%20%5C%5C%20%5Csum_%7Bk%3D1%7D%5E%7Bn%7Dk%5E3%20%3D%20%5Cfrac%7Bn%5E2%28n&plus;1%29%5E2%7D%7B4%7D%20%5Cquad%5C%3B%5C%3B%20%5Cend%7Bmatrix%7D)

## Geometric series
For real `x != 1`, the summation

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D0%7D%5E%7Bn%7Dx%5Ek%20%3D%201%20&plus;%20x%20&plus;%20x%5E2%20&plus;%20%5Ccdots%20&plus;%20x%5En%20%5Cend%7Bmatrix%7D)

is a **geometric** or **exponential series** and has the value

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D0%7D%5E%7Bn%7Dx%5Ek%20%3D%20%5Cfrac%7Bx%5E%7Bn&plus;1%7D-1%7D%7Bx-1%7D%20%5Cend%7Bmatrix%7D)

When the summation is infinite and `|x| < 1`, we have the infinite decreasing geometric series

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D0%7D%5E%7B%5Cinfty%7Dx%5Ek%20%3D%20%5Cfrac%7B1%7D%7B1-x%7D%20%5Cend%7Bmatrix%7D)

## Harmonic series
For positive inegers n, the n-th **harmonic number** is

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20H_n%20%3D%201%20&plus;%201/2%20&plus;%201/3%20&plus;%201/4%20&plus;%20%5Ccdots%20&plus;%201/n%20%5C%5C%20%5C%5C%20%3D%20%5Csum_%7Bk%3D1%7D%5E%7Bn%7D1/k%5Cqquad%5Cqquad%5Cqquad%5Cqquad%20%5C%5C%20%5C%5C%20%3D%20ln%5C%3Bn%20&plus;%20O%281%29%5Cqquad%5Cqquad%5Cqquad%5Cquad%20%5Cend%7Bmatrix%7D)

## Telescoping series
For any sequence `a_0, a_1, ..., a_n`,

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D1%7D%5E%7Bn%7D%28a_k-a_%7Bk-1%7D%29%20%3D%20a_n%20-%20a_0%20%5Cend%7Bmatrix%7D)

since each of the terms is added in exactly once and subtracted out exactly once. We say that the sum **telescopes**. As an example of a telescoping sum, consider the series

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D1%7D%5E%7Bn-1%7D%5Cfrac%7B1%7D%7Bk%28k&plus;1%29%7D%20%5Cend%7Bmatrix%7D)

Since we can rewrite each term as

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Cfrac%7B1%7D%7Bk%28k&plus;1%29%7D%20%3D%20%5Cfrac%7B1%7D%7Bk%7D%20-%20%5Cfrac%7B1%7D%7Bk&plus;1%7D%20%5Cend%7Bmatrix%7D)

we get

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Bmatrix%7D%20%5Csum_%7Bk%3D1%7D%5E%7Bn-1%7D%20%5Cfrac%7B1%7D%7Bk%28k&plus;1%29%7D%20%3D%20%5Csum_%7Bk%3D1%7D%5E%7Bn-1%7D%5Cbigl%28%5Cbegin%7Bsmallmatrix%7D%20%5Cfrac%7B1%7D%7Bk%7D%20-%20%5Cfrac%7B1%7D%7Bk&plus;1%7D%20%5Cend%7Bsmallmatrix%7D%5Cbigr%29%20%5C%5C%20%5C%5C%20%5Cquad%5C%3B%5C%3B%3D%201%20-%20%5Cfrac%7B1%7D%7Bn%7D%20%5Cend%7Bmatrix%7D)
