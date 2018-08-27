---
layout: post
date:   2018-06-06
categories: theoretical-cs
tags: self-study interview-prep
---
[1]: https://www.amazon.com/Cracking-Coding-Interview-Programming-Questions/dp/098478280X "Cracking the coding interview (5th edition)"
[2]: https://www.amazon.com/Elements-Programming-Interviews-Java-Insiders/dp/1517671272/ref=pd_sim_14_3?_encoding=UTF8&pd_rd_i=1517671272&pd_rd_r=XEQKDVEBYF5DAWEC6B7W&pd_rd_w=8RwvQ&pd_rd_wg=L59s8&psc=1&refRID=XEQKDVEBYF5DAWEC6B7W&dpID=51EjkypFE0L&preST=_SY291_BO1,204,203,200_QL40_&dpSrc=detail "Elements of Programming Interviews (in Java)"
[3]: https://stackoverflow.com/a/6165124/2259743 "Memoization and tabulation"
[4]: https://stackoverflow.com/a/12134358/2259743 "Fibonacci in all possible ways"
[5]: https://stackoverflow.com/a/13538715/2259743 "DP vs divide and conquer"
[6]: https://en.wikipedia.org/wiki/Dynamic_programming#Computer_programming "Wiki on DP"

The second part on my quick revision on data structures and algorithms, this one focuses on algorithms.


### Design techniques
Based off of [1]:
- Examplify: write specific examples of the problem and derive a general rule from them.
  - Some times it helps to start with an intermediate state of the problem e.g. 3.6 in [1]. Don't forget to check the edge cases (i=0 and i=n) at the end anyway.
- Pattern matching: is it a variation of a known algo? or can you use the same technique?
- **Simplify** and generalize: change a constraint to make the problem easier, solve and then generalize
  - E.g. in 18.7 of [1] assume a word can be created by 2 words and not n
- Base case and build: start from a base case (e.g. n = 1) and build next solutions from it. 2 variants here: top down or bottom up.
- Brainstorm: with data structures
- Bonus: Brainstorm with complexities: if you know already the desired complexity of the solution, think of an algo that has the same complexity


#### Tips
+ Pre-processing: instead of doing everything in one go (which could be n^2), consider pre-processing first, e.g.
  + sorting input first
  + construct an "efficient" data structure (depending on the problem e.g. heap/tree/hashmap etc) and then act on it with lookups/insertions etc.
    + e.g. create the suffix tree first in 18.8 of [1]. Here the data structure is created from the problem inputs.
    + e.g. create a character lookup hashmap in 1.1 of [1]. Here the data structure does not use directly the problem inputs.
  + Split in 2 or more tasks that are running sequentially: the total complexity will the worst of the 2.
    + e.g. generate sequences and then evaluate them e.g. in Palantir interview.
+ Time vs space tradeoff: you can improve time by using up more space and vice versa. Usually we care more about time but check with the interviewer.
+ Runner up pointer technique: "offset" the second pointer by a certain amount or have it move twice (or as much as needed) as fast `fast = fast.next.next;`, for example used in palindromes, rotated lists, segregating arrays



### Recursion vs DP vs divide and conquer
Note: the terms are usually used ambiguously.
+ Recursion is simply the act of calling the same function.  
  + I find it's more bottom-up than top-down e.g. in trees although you start from the root, it's when you reach a leaf (where the answer is trivial) that you start bubbling up the answer.
  + Every recursive solution (if) [can be coded](https://stackoverflow.com/questions/2093618/can-all-iterative-algorithms-be-expressed-recursively) as an iteration (for/while), which might be more space efficient - see [part I]({{ site.baseurl }}{% post_url 2018-06-04-Interview-prep-intro:-Data-structures-and-complexity %}) for a note on tail optimization.
  + When writing recursive functions note the ending conditional and the base calculation (besides what is being returned and the input like with any method)
    + A limitation (with Java at least) is that you can't return multiple values (unless with a wrapper object or static "globals")

+ Dynamic programming is solving problems with overlapping subproblems by solving each sub-problem only **once**. [5]  
  + To achieve solving only once DP solutions use memoization or tabulation [3]. Memoization is simply caching, tabulation is ordering subproblems appropriately.
  + Recursion + memoization = one way for DP [1], [5]. [1] (incorrectly) considers this the only type of DP. Another way is iteration + tabulation [3], [4].

+ If a problem can be solved by combining optimal solutions to non-overlapping sub-problems, the strategy is called "divide and conquer" instead. This is why merge sort and quick sort are not classified as dynamic programming problems. [6]

TODO: gather also from chapter 11 of Algorithms in a nutshell



## Types of problems
- Check if a condition exists
  + Output is true or false. Examples:
    + Given 2 strings return if they are isomorphic/palindromic/permutation/substring etc (leetcode 205, 1.3 in [1]. 1.8 in [1])
    + Given an array of integer return if there is k-sum pair
  + Return **an** example or all:
    + Return the pair in the k-Sum example or all pairs.
    + Return all sequences.
- Optimization problem: longest/shortest subarray
  + Usually DP
+ Duplicates/uniques
+ rotations: 1.8, 11.3
+ palindromes: [1] 2.7,
+ anagrams (or permutations for strings): [1] 1.3,11.2
+ permutations/combinations: detect them, find where they occur, compute all etc


### Specific to data-structures
+ Strings
  + On string problems (e.g. longest palindrome Leet 5) order usually matters (so you can't pre-sort for example)
  + Think of precomputed dicts (e.g. unique characters of string 1.1 in Ctci)
  + Lookup many (small) strings in one: build a suffix tree 18.8 in Ctci
  + Lookup a String in a big text? Rabin-karp 6.13 in Epi
+ Arrays:
  + k-sum with variations ([k-diff](https://leetcode.com/problems/k-diff-pairs-in-an-array/description/), using 2, 3, 4 numbers), detect if there is a pair, return the pairs. Array can be sorted or not. Youtube from Google, CTci 17.12
  + 2 pointers technique e.g. Dutch flag
+ LinkedList
  + 2 pointers technique: 2.2 k-th, 2.6 loop, 2.7 palindrome check.
  + Recursive algorithms, almost the same as in Trees/Graphs that are represented by objects and pointers.
  + Check: Singly or doubly linked? Do we know the size?


## Resources
+ Algorithms in a nutshell, Edition 1 and [2nd in PDF](https://doc.lagout.org/science/0_Computer%20Science/2_Algorithms/Algorithms%20in%20a%20Nutshell%20%282nd%20ed.%29%20%5BHeineman%2C%20Pollice%20%26%20Selkow%202015-11-25%5D%20%28draft%29.pdf), [Github repo](https://github.com/heineman/algorithms-nutshell-2ed), check Java/Python code folders
+ [Clevercoder](https://clevercoder.net/2017/09/04/toptal-passed-interview/) and [here](https://clevercoder.net/2017/09/24/technical-interviews-prepare/)
+ [Toptal](https://www.toptal.com/algorithms/interview-questions)
+ [Interviewbit](https://www.interviewbit.com/dashboard/)
+ [Programcreek](https://www.programcreek.com/2012/11/top-10-algorithms-for-coding-interview/)
+ Codility
+ Algorithms in a nutshell version 2: https://doc.lagout.org/science/0_Computer%20Science/2_Algorithms/Algorithms%20in%20a%20Nutshell%20%282nd%20ed.%29%20%5BHeineman%2C%20Pollice%20%26%20Selkow%202015-11-25%5D%20%28draft%29.pdf
