# Laws of Software Development

## Murphy's Law

```
If something can go wrong, it will.
```

Defensive programming, version control, doom scenario, TDD, MDD, etc. are all good practices to defend against this law.

## Brook's Law

```
Adding manpower to a late software project makes it later.
```

If a project is running late, simply adding manpower will most likely have disastrous results. Looking and reviewing the level of programming efficiency, the software methodology, the technical architecture, etc. will almost always have better outcomes.

## Hofstadter's Law

```
It always takes longer than you expect, even when you take into account Hofstadter's Law.
```

It is very difficulty to accurately estimate the time it will take to complete tasks of substantial complexity.

Always have a buffer before you give any sort of estimation.

## Conway's Law

```
Any piece of software reflects the organizational structure that produced it.
```

It's hard for someone to change something if the thing he/she wants to change is owned by someone else.

It is much better, and more and more implemented to deploy teams around a bounded context. Architectures like microservices structure their teams around service boundaries rather than siloed technical architecture partitions.

Structure teams to look like your target architecture.

## Postel's Law

Also known as **Robustness principle**

```
Be conservative in what you send, be liveral in what you accept.
```

Originally coined as a principle to make TCP implementations robust. In today's highly charged political environment, Postel's law is a uniter.

## Pareto Principle

```
For many phenomena, 80% of consequences stem from 20% of the causes.
```

80% of the bugs in the code arise from 20% of the code. 

80% of the work done in a company is performed by 20% of the staff.

The problem is you don't always have a clear idea of which 20%.

## Peter Principle

```
In a hierarchy, every employee tends to rise to his level of incompetence.
```

The Peter Principle is based on the logical idea that competent employees will continue to be promoted, but at some point will be promoted into positions for which they are incompetent, and they will then remain in those positions because of the fact that they do not demonstrate any further competence that would get them recognized for additional promotion. According to the Peter Principle, every position in a given hierarchy will eventually be filled by employees who are incompetent to fulfill the job duties of their respective positions.

## Kerchkhoff's Principle

```
In cryptography, a system should be secure even if everything about the system, except for a small piece of information - the key - is public knowledge.
```

The main principle underlying public key cryptography.

## Linus' Law

```
Given enough eyeballs, all bugs are shallow.
```

The more widely available the source code is for public testing, scrutiny, and experimentation, the more rapidly all forms of bugs are discovered.

## Moore's Law

```
The power of computers per unit cost doubles every 24 months.
```

or

```
The number of transistors on an integrated circuit will double in 18 months.
```

or

```
The processing speed of computers will double every two years.
```

## Wirth's Law

```
Software gets slower faster than hardware gets faster.
```

This law goes against Moore's Law.

## Ninety-ninety Rule

```
The first 90% of the code takes 10% of the time. The remaining 10% takes the other 90% of the time.
```

## Knuth's Optimization Principle

```
Premature optimizations is the root of all evil.
```

1. Write code.
2. Identify bottlenecks.
3. Fix.
