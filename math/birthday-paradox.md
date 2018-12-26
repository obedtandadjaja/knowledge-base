# Birthday Paradox
How many people must there be in a room before there is a 50% chance that 2 of them were born on the same day of the year?

We ignore the issue of leap years and assume that all years have `n = 365` days.

For `i = 1, 2, ..., k`, let b_i be the day of the year on which person i's birthday falls, where 1 <= b_i <= n.

We also assume that birthdays are uniformly distributed across the n days of the year so that `Pr{b_i = r} = 1/n`.

The probability that at least two of the birthdays match is 1 minus the probability that all birthdays are different:

![equation](http://latex.codecogs.com/gif.latex?%5Cbegin%7Balign*%7DPr%5Cleft%20%5C%7B%20B_k%20%5Cright%20%5C%7D%20%26%3D%20%28%5Cfrac%7B365%7D%7B365%7D%29%20%5Ccdot%20%28%5Cfrac%7B364%7D%7B365%7D%29%20%5Ccdot%20%28%5Cfrac%7B363%7D%7B365%7D%29%20%5Ccdots%20%28%5Cfrac%7B365-i%7D%7B365%7D%29%20%5C%5C%20%26%3D%201%5Ccdot%20%5Cleft%20%28%20%5Cfrac%7Bn-1%7D%7Bn%7D%20%5Cright%20%29%20%5Ccdot%20%5Cleft%20%28%20%5Cfrac%7Bn-2%7D%7Bn%7D%20%5Cright%20%29%20%5Ccdots%20%5Cleft%20%28%20%5Cfrac%7Bn-k&plus;1%7D%7Bn%7D%20%5Cright%20%29%20%5C%5C%20%26%3D%201%5Ccdot%20%5Cleft%20%28%201%20-%20%5Cfrac%7B1%7D%7Bn%7D%20%5Cright%20%29%20%5Ccdot%20%5Cleft%20%28%201%20-%20%5Cfrac%7B2%7D%7Bn%7D%20%5Cright%20%29%20%5Ccdots%20%5Cleft%20%28%201%20-%20%5Cfrac%7Bk-1%7D%7Bn%7D%20%5Cright%20%29%20%5C%5C%20%5Cend%7Balign*%7D)

The above expression can be approximated using **Taylor’s Series**.

![equation](http://latex.codecogs.com/gif.latex?e%5E%7Bx%7D%3D1&plus;x&plus;%5Cfrac%7Bx%5E%7B2%7D%7D%7B2%21%7D&plus;...)

provides a first-order approximation for ex for x < 1:

![equation](http://latex.codecogs.com/gif.latex?e%5E%7Bx%7D%5Capprox%201&plus;x)

To apply this approximation to the first expression derived for `Pr{B_k}`, we substitute `x = -i/365`, thus

![equation](http://latex.codecogs.com/gif.latex?%5CLarge%7Be%5E%7B%5Cfrac%7B-i%7D%7B365%7D%7D%5Capprox%201-%5Cfrac%20%7Bi%7D%7B365%7D%7D)

By putting the value of 1 – i/365 as e^(-i/365), we get following.

![equation](http://latex.codecogs.com/gif.latex?%5Capprox%201%5Ctimes%20e%5E%7B%5Cfrac%7B-1%7D%7B365%7D%7D%5Ctimes%20e%5E%7B%5Cfrac%7B-2%7D%7B365%7D%7D...%5Ctimes%20e%5E%7B%5Cfrac%7B-%28i-1%29%7D%7B365%7D%7D)

![equation](http://latex.codecogs.com/gif.latex?%5Capprox%201%5Ctimes%20e%5E%7B%5Cfrac%7B-%281&plus;2&plus;...&plus;%28i-1%29%29%7D%7B365%7D%7D)

![equation](http://latex.codecogs.com/gif.latex?%5Capprox%201%5Ctimes%20e%5E%7B%5Cfrac%20%7B-%28i%28i-1%29%29/2%7D%7B365%7D%7D)

By taking Log on both sides, we get the reverse formula.

![equation](http://latex.codecogs.com/gif.latex?n%20%5Capprox%20%5Csqrt%7B2%20%5Ctimes%20365%20ln%5Cleft%20%28%20%5Cfrac%7B1%7D%7B1-probability%7D%20%5Cright%20%29%7D)

#### Pseudocode
```java
public double find(double p) { 
    return Math.ceil(Math.sqrt(2 *  365 * Math.log(1 / (1 - p)))); 
} 
```
