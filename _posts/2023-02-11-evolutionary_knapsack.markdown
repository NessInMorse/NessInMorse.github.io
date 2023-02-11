---
layout: post
title:  "Creating a solution for the Knapsack problem with Evolution"
categories: password bad-usb
permalink: "/serial-bridge/"
show_excerpts: True
---

Creating a solution for the Knapsack problem by working with Evolution

# Background
The Knapsack problem is a theoretical problem in computer science, where an imaginary traveller finds a great treasure but only holds a knapsack in which only a few valuable items can be inserted - [you can find more about the knapsack problem here](https://en.wikipedia.org/wiki/Knapsack_problem).
Not every treasure is created that can be inserted is created equal however and when the total weight of the treasures collected exceeds the maximum weight the knapsack can hold, the knapsack rips apart and the traveller is left with no treasure at all!
The goal of the problem is to find the optimum combination of values so that the traveller has the maximum amount of value to take home!

This problem is seemingly very for small values, such as having only 4 to 8 items. When the numbers of treasures start increasing however, the problem starts to get increasingly difficult. With 64 possible treasures to hold, there are a total of 2<sup>64</sup> (=18446744073709551616) combinations!


# Introduction to Evolutionary Algorithms
Evolutionary Algorithms is a subclass of algorithms based on evolution which is found in biology. It revolves around changing aspects of individuals within a population ever so slightly by the recombination of chromatids from your parents, and the slight chance of a mutation. With these two factors, recombination and mutation, there is a TON of variety in the individuals in the population. And this variety or - greatness of gene pool - is used to our advantage in the use of evolutionary algorithms.

One of the key aspects why evolutionary algorithms are decent for the knapsack problem is that it is easily programmed, Machine Learning models can often be difficult to program, sometimes taking multiple weeks(!) With evolutionary algorithms however it can be programmed in a few hours to a few days! There are some other pros and cons to using evolutionary algorithms, that I will not go into detail here, but I'll leave a table with the summary of those pros and cons.

| Pros | Cons |
|------|------|
| Fast to program | Slow to run |
| Multiple good solutions | No perfect solution / answer |
| Parallelizeable | Inefficient memory usage |


