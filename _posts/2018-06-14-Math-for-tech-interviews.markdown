---
title: "Tech interviews: Math special"
layout: post
date: 2018-06-14
categories: theoretical-cs
tags: self-study interview-prep
---
[1]: https://www.amazon.com/Cracking-Coding-Interview-Programming-Questions/dp/098478280X "Cracking the coding interview (5th edition)"
[2]: https://www.amazon.com/Elements-Programming-Interviews-Java-Insiders/dp/1517671272/ref=pd_sim_14_3?_encoding=UTF8&pd_rd_i=1517671272&pd_rd_r=XEQKDVEBYF5DAWEC6B7W&pd_rd_w=8RwvQ&pd_rd_wg=L59s8&psc=1&refRID=XEQKDVEBYF5DAWEC6B7W&dpID=51EjkypFE0L&preST=_SY291_BO1,204,203,200_QL40_&dpSrc=detail "Elements of Programming Interviews (in Java)"

A special post on math related questions for technical interviews. Like discussed in the [introduction]({{ site.baseurl }}{% post_url 2018-06-04-Interview-prep-intro:-Data-structures-and-complexity %}) of this series, for my preparation I used mostly [1]:Cracking the coding interview and [2]:Elements of programming interviews. [1] has a special chapter dedicated on math which is a great starting point. Most of the material here though come from Chapter 9.

My main issue in both books as well as solutions online (e.g. Geeksforgeeks etc) is that a certain method was used in some problems (e.g. counting) and another on others (e.g. combinations formula) without proper explanation as to why use one vs the other. I couldn't figure which one to use. I didn't want to have to memorize problems and solutions so I tried to express one in terms of the other and I realized that the expressions are actually equivalent.

In this post, I will attempt to re-organize the material presented and go deeper in certain aspects using additional resources as needed. I will not discuss brain teasers or the math required to estimate complexities.


## TL;DR:
Most math problems can be solved using counting, combinatorics or probabilities. These methods are equivalent and could be used interchangeably but arguably for certain problems some might be easier to understand than others.


## Types of questions
I have identified 3 types of questions where math can be useful.
+ Counting: how many ways are there to do X task e.g. 9.1, 9.2, 9.8 in [1]
+ Calculating probability (which can be reduced to counting problems): # matching results/# all results.
+ Enumerate: find all anagrams etc. e.g. 9.4, 9.5 in [1]

These problems can be further modified by:
+ Is there replacement of elements? After selection, do we put the items back or not? Throwing a dice is an example of not replacing elements.
+ Are there duplicate elements or does order of the **selected** items matter? Emphasis on selected, see later why.
+ Is each selection an independent event? In other words can an existing selection affect the next element to be selected?


### Probabilities
I list probabilities first not because they are the most useful or commonly asked but because they form a good foundation of terms to formally explain the counting problems. They also look the most "scientific" to me :) [Wiki says](https://en.wikipedia.org/wiki/Probability):
> Probability is the measure of the likelihood that an event will occur.

The basics  
  + $$P(A\cap B) = P(B given A)P(A)$$, intersection = conditional * P(A)
  + $$P(A\cup B) = P(A) + P(B) - P(A\cap B)$$, union

Based on those we can derive the following special cases:
  + Mutually exclusive events: $$P(A\cap B) = 0$$, if one happens the other can't happen
  + Independent: $$P(A\cap B) = P(A)P(B)$$ because the conditional probability of B is P(B)

![Difference of mutually exclusive vs independent]({{ "/assets/math/venn.jpg" | absolute_url }})

Notice how independent events are not overlapping, they placed in different layers. Independent events are for example throwing 2 free rows (the first does not affect the second) or picking a marble out of a set and then replacing it back.


### Counting methods
As we said above counting and probability by definition are related. Most of the times it's easier to count and from that derive the probability if needed.

The most common technique I have encountered is the slots technique: how many choices do you have at slot 0: n, 1: n-1 ... n: 1 => n! This is for events without replacement (e.g. in anagrams of strings you can use a letter from a string only once - if the letters are unique)

The main cases are
+ Addition: we need to choose from different sets, example choose
+ Multiplication: we choose from the same set
  + This is used in overcounting too.
+ Exponention (or multiplication with the same number): from the same set with replacement

To calculate this more formally you can use..

### Combinatorics
According to [wiki](https://en.wikipedia.org/wiki/Combinatorics) combinatorics is the area of mathematics concerned with counting - great! 2 big categories here:

+ Combinations formally are $$ \frac{n!}{(n-r)! r!}$$ e.g. you have 10 balls, in how many ways can you pick 3 red balls (aka n choose k or [binomial coefficient](https://en.wikipedia.org/wiki/Binomial_coefficient)). The order of how you pick those 3 red balls doesn't matter because they are the same balls.
  + This is the difference between combinations and permutations: does the order of the *selected* items matter (only selected, not between selected and not selected)? If yes, permutation, if no combination [source](https://gmatclub.com/forum/permutations-and-combinations-simplified-150835.html)  
      + Another good example is question 9.2 from [1]
+ Permutations are the bigger number, formally $$ \frac{n!}{(n-r)!}$$ e.g. in how many ways can medals be awarded, picking numbered balls, count anagrams of strings..
  + Alternatively think of: options at first slot, then 4 etc.. 5!
  + [Permutations special cases](https://www.khanacademy.org/math/precalculus/prob-comb/combinations/e/permutations_and_combinations_2)
  + Permutations with one element duplicated: divide result by k! where k is the number of duplicates. k! is the number of arrangements
    + This is the same we do with combinations, remove the overcounting!
  + Permutations with affinity constraints: treat as one and then account for their inner order. For the opposite (nodes should be apart): substract the affinity result from the total.

## Interchanging methods to arrive to the same solution
This is the interesting part in my opinion.

For example, in order to counting the number of paths in a grid (9.2 in [1] or [here](https://betterexplained.com/articles/navigate-a-grid-using-combinations-and-permutations/)), seen as a string of X+Y length. Seen as "rrruuur" it's similar to n choose k where n = X + Y
This is a combination problem not a permutation: yes the order is important for example "rur" is different than "urr" although both end up in the same place. What matters the order of the selected items! In that sense "rur" is the same with the "r"s flipped.
+ You can use the combination formula. Note that the formula is exactly the same whether you choose the rights or the ups out of the "bag" of movements.
+ Or you can choose the permutation formula and divide by the overcounting.
+ To convert this to a probability divide the number of ways to get there with the total paths. Those are 2^n. Why? At each point you have a choice of 2 directions. Each selection is independent by previous ones. So for n selections 2^n.

9.4 in [1] asks for all the subsets: combinations. To count how many they are we can use counting:
+ Each element has the choice of being there or not. The choice for one element does not affect the following ones.
What is the probability of a number being in the set n / 2n

9.5 to find the # of permutations: order matters
if we assume unique characters n!
if we have duplicates n!/k! where k is the #of duplicate numbers



## Appendix: Other math related problems
+ Primes: a number > 1 who is divided only by itself and 1
  + Checking for primality: O(sqrt(n))
  + Generating a list of primes aka Sieve of Eratosthenes: O(nlog(logn))
+ Series
  + Arithmetic (adding a step): $$ \sum_{i=1}^n a = n\frac{a_1+a_n}{2} $$ where d is the step
  + Geometric (multiplying by a step): $$ \frac{a_1-a_1r^n}{1-r} $$ where r is the step
  + An interesting application of these formulas is in finding the missing element on a series TODO
+ GCD and LCM: I won't cover those.
