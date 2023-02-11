---
layout: post
title:  "Creating a solution for the Knapsack problem with Evolution"
categories: password bad-usb
permalink: "/knapsack-problem/"
show_excerpts: True
---

Creating a solution for the Knapsack problem by working with Evolution in Julia!

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

By using integers as representation of our chromosomes, we can use integer operations which are lightyears faster than vector operations in Julia, and we will need that light speed so that we can blaze through the simulation, but I'll get to that in a second.

One problem that arises by using integers unfortunately, is that we will be limited by the size of integers of the language and since I was unsure, I did not want to go greater than 64-bit integers, as I was scared that some ['unintended behaviour'](https://github.com/JuliaLang/julia/issues/3081) would act up.
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

The `max_weight` was set to 2500, this means that on average, 25 out of 64 items could fit in the knapsack (since on average, any given item would weigh 100)

| variable | size | 
|----------|------|
| maximum_weight | 2500 | 




# Making the program branchless
One of the reasons to make your program branchless is so that is faster, oftentimes at the cost of readability. Branching in code is expensive so we want to avoid it as much as possible.
And since we are working with predominantly integers (apart from the mutation value). Making the program branchless is not very difficult.

One place where the readability took a great hit was in the `recombine_chromosomes!()` function, where it is a LOT of integer operations.
```julia
function recombine_chromosomes!(chromosome_a::Int64,
                                chromosome_b::Int64,
                                chromosome_size::Int8,
                                mutation_chance::Float32)
        #=
        Creates new recombinants of the two chromosomes provided
        Also mutates a single chromosome based on the mutation chanced provided.
        in:
                2 chromosomes:  a and b.
                chromosome_size: a size (in bits) of a chromosome
                mutation_chance: the chance of a mutation occurring in any gene within the chromosome
        out:
                2 recombinant chromosomes. based on the two parents a and b and the mutations.
        =# 
        chromatid_length = chromosome_size >> 1
        offset = 64 - chromatid_length

        chromatid1 = (chromosome_a << offset) >>> offset
        chromatid2 = (chromosome_a >>> chromatid_length) << chromatid_length
        
        
        chromatid3 = (chromosome_b << offset) >>> offset
        chromatid4 = (chromosome_b >>> chromatid_length) << chromatid_length
        
        new_chromosome1::Int64 = chromatid1 + chromatid4
        new_chromosome2::Int64 = chromatid2 + chromatid3

        mutant1 = (rand() < (mutation_chance / 2)) * rand(1:chromosome_size)
        mutant2 = (rand() < (mutation_chance / 2)) * rand(1:chromosome_size)

        
        new_chromosome1 = new_chromosome1 ⊻ (1 << mutant1)
        new_chromosome2 = new_chromosome2 ⊻ (1 << mutant2)


        return new_chromosome1, new_chromosome2
end
```

How the function works is that it quite literally shaves off either the start or end of the integer and then combines it with the other recombinant. So the recombination works as follows:
```
chromosome AB
chromosome CD

new chromosome1 AD
new chromosome2 CB
```
The mutation is done by using XOR on a specific bit, the mutant is only active when a random integer is lower than the mutation chance divided by 2 (it is divided by two because it is called on both chromosomes).
The table for [XOR](https://en.wikipedia.org/wiki/XOR_gate) works as follows, I will use `G` to represent the gene, `M` to represent the mutation and `O` to represent the new gene:
| G | M | O |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 | 

Here we can see that XOR only mutates the output O when M is `1`.


# Results
Since the values and the weights were always chosen randomly there was no _consistent_ result, however, oftentimes it did exceed a value of 4,000. Without exceeding the maximum weight of 2500!
![output](https://user-images.githubusercontent.com/56824499/218279636-a9d36b32-09dc-4d04-ae39-5ebf60b886ef.svg)


# Performance
I wanted it to be fast, and I believe I have succeeded. 

Here are 5 runs of the program. 
| Time | Allocated memory | 
| 0.015967 seconds | 259.176k allocations 11.270 MiB | 
| 0.028990 seconds | 186.38k allocations 10.168 MiB, 58.26% compilation time | 
| 0.016459 seconds | 259.77k 11.280 MiB | 
| 0.033262 seconds | 259.38 k allocations: 11.273 MiB, 49.16% gc time | 
| 0.014795 seconds | 261.44 k allocations: 11.307 MiB | 



These settings where used:
| variable | value |
|----------|-------|
| population size | 30 |
| top performers | 4 |
| mutation rate | 0.03 |
| generations | 1250 |
| max weight | 2500 |
| chromosome size | 64 |
| value list size | 64 |
| weight list size | 64 |

I ran it with more generations as well, and it seemed to perform at around 60.000-95.000 generations per second, that is 1,900,000 years of evolution per second!


# Code


