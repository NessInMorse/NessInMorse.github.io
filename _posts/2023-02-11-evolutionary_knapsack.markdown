---
layout: post
title:  "Creating a solution for the Knapsack problem with Evolution"
categories: password bad-usb
permalink: "/knapsack-problem/"
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

# Translation from problem to program
The question you might be asking is. Alright, this is great and all, but how are we going to translate all of this information and let evolution run?
Great you asked imaginary viewer! In the simulation of our problem, we will use integers as our 'chromosomes', the bits in the integer will act as the genes on our chromosome.
A 1 will signify that the gene is 'on' and a 0 will indicate that a gene is 'off'. Perhaps this is a *bit* complicated, so perhaps an example will make it easier.
Consider the number 42, the binary representation of 42 will be our gene.

```julia
bitstring(42)
"0000000000000000000000000000000000000000000000000000000000101010"
```

We read the chromosome from right to left, so for 42 the first gene is a 0 so it is OFF, the second gene is ON, the third is a 0 so it is OFF. The fourth is ON again.

The genes in this context are our treasures, a 1 signifiies that we attempt to take the treasure, and a 0 signifies we do not take it.

By using integers as representation of our chromosomes, we can use integer operations which are lightyears faster than vector operations in Julia, and we will need that light speed, but I'll get to that in a second.

One problem that arises by using integers unfortunately, is that we will be limited by the size of integers of the language and since I was slightly unsure, I did not want to go greater than 64-bit integers.
This limit of 64 bits means that the maximum length of the list of treasures will also only be 64.
So there will only be 2<sup>64</sup> combinations of treasures to take.

# Asigning the fitness of the population

To asign fitness to each of the individuals in the population we run the following function.

```julia
function calculate_fitness(chromosome::Int64, 
                           chromosome_size::Int8,
                           values::Vector{Int64},
                           weights::Vector{Int64},
                           max_weight::Int64)
        #=
        Calculates the fitness of any given individual's chromosome
        With the weights and values given in the dataset.
        When the weight is over the maximum weight however, the fitness will be zero

        in:
                chromosome: a chromosome containing the building blocks
                chromosome_size: the size of the chromosome
                values: the different values all of the knapsack items have
                weights: the different weights all of the knapsack items have
                max_weight: the maximum weight a knapsack is able to hold
        out:
                the fitness (and value) the knapsack-chromosome has.
        =# 
        weight = 0
        value = 0
        for n = 1:chromosome_size
                # a binary way of finding whether any given
                # bit is not zero
                n_bit = (chromosome & (1 << n) != 0)
                weight += n_bit * weights[n]
                value += n_bit * values[n]
        end
        return (weight < max_weight) * value
end
```

The function reads a specific bit each time and puts that in `n_bit` and then creates the product of that bit with the weight gene on that chromosome and the value of that gene.
As you can see, no if-statements are used here, which means that the calculation with the bit is always performed. Though, when the gene is off (a zero), the multiplication will also result in 0, so there will not be an alteration to the weight variable, or the value variable.

In the final return statement the weight is evaluated, whether it is lower than the maximum weight value possible for the knapsack. Remember, when it exceeds that, the knapsack is going to rip apart and we will have a fitness of zero! When it *is* lower than zero, the value will be returned.

# We have the fitness, now what?
When we have the fitness of all the individuals in the population we want to find the best performers to create a new population.
For this program I used a population size of 30 and I found the top 4 performers to repopulate the entire next generation.



This meant that every generation will have 30 individuals stemming from the top 4 performers in the last generation.

Not to forget however, a mutation rate! Without mutation our model will not accurately represent nature, but more importantly, it will rest in a local optimum without ever having a way of getting out. By using mutation rates and enough generations, we have the possibility to step out of a local optimum and get to an even higher point. 
For this program I used a mutation rate of 0.03, since it seemed to provide the best results in many runs (without being overly inconsistent).
The mutation will act on the chromosomes that are added into the new population and have a chance of 0.03 to swap a single gene from 0 to 1, or from 0 to 1. 

Even after the mutation rate, the program often found an optimum solution after around 1000 generations. So the amount of generations I will stick to is 1250.

| variable | value |
|----------|-------|
| population | 30 |
| top performers | 4 |
| mutation rate | 0.03 |
| generations | 1250 |


I also needed to add the environment the population would run into, it is as follows. It was limited and through multiple runs seemed to get decent results:
These are the values of each of the treasures that can be found.

| variable | length (amount of values) | possible values |
|----------|--------|-----------------|
| values  | 8, 16, 32, 64 | 1 - 200 | 
| weights | 8, 16, 32, 64 | 1 - 200 | 

The max_weight was set to 2500, this means that on average, 25 out of 64 items could fit in the knapsack (since on average, any given item would weigh 100)

| variable | size | 
|----------|------|
| maximum_weight | 2500 | 




# Making the program branchless
One of the reasons to make your program branchless is so that is faster, oftentimes at the cost of readability. Branching in code is expensive so we want to avoid it as much as possible.
And since we are working with predominantly integers (apart from the mutation value). Making the program branchless is not very difficult.



