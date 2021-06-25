---
title: "Asymptotic Complexity Explained"
date: 2021-06-24T16:28:43-05:00
katex: true
draft: true
---

# A Brief Introduction to Asymptotic Complexity

> “Simplicity is a great virtue but it requires hard work to achieve it and education to appreciate it. And to make matters worse: complexity sells better.”
>
> -- Edsger W. Dijkstra

Let’s start with [computational complexity](https://en.wikipedia.org/wiki/Computational_complexity). The computational complexity of an algorithm is a measure of the resources the algorithm needs to run. These resources are typically significant considerations to the real world application of the algorithm such as the running time and space in memory used by the algorithm.

We can think of the time complexity of an algorithm being a function \\( T(n) \\) where \\(n\\) is the input to the algorithm. The asymptotic complexity of \\( T(n) \\) is how this function grows for larger and larger inputs (or how it behaves as it gets closer and closer to the asymptote).

To describe asymptotic complexity, we use [Big-O notation](https://xlinux.nist.gov/dads/HTML/bigOnotation.html) which allows us to talk precisely about the limiting behavior of a function. This "limiting behavior" is how the function behaves as the input n approaches \\( \infty \\).

I'll quickly describe Big-O, Big-\\(\Omega\\), and Big-\\(\Theta\\) but there are lots of other notations that exist like little-o and so on.

## Big-O

We use Big-O to denote an asymptotic upper bound on a function i.e. a bound on the worst case. "It can't get worse than this."

To better understand what this means, below is the formal definition of Big-O.

\\[ O(g(n)) = \\{f(n) \mid \exists \ c, n_0 >0 \ \mathrm{s.t.} \ 0 \leq f(n) \leq cg(n) \ \forall n \geq n_0 \\} \\]

To put it simply, \\(   \\) ...

Ex. If our running time function is \\( T(n) = 3n^2 + 19n + 9\\), then \\( O(n^2), O(n^3), ... \\) would all be valid upper bounds of \\( T(n) \\).

!["Life could be worse, Calvin." "Life could be a lot better too!"](/calvin_life_could_be_worse.jpg)

## Big-\\(\Omega\\)

## Big-\\(\Theta\\)
