---
layout: post
categories: theoretical-cs
tags: self-study interview-prep
---
[1]: https://www.amazon.com/Cracking-Coding-Interview-Programming-Questions/dp/098478280X "Cracking the coding interview (5th edition)"
[2]: https://www.amazon.com/Elements-Programming-Interviews-Java-Insiders/dp/1517671272/ref=pd_sim_14_3?_encoding=UTF8&pd_rd_i=1517671272&pd_rd_r=XEQKDVEBYF5DAWEC6B7W&pd_rd_w=8RwvQ&pd_rd_wg=L59s8&psc=1&refRID=XEQKDVEBYF5DAWEC6B7W&dpID=51EjkypFE0L&preST=_SY291_BO1,204,203,200_QL40_&dpSrc=detail "Elements of Programming Interviews (in Java)"
[3]: "Algorithms in a Nutshell"
[4]: http://steve-yegge.blogspot.com/2008/03/get-that-job-at-google.html "Get that job at Google"

The third part of my interview preparation focusing on searching and sorting. Find the introduction on data structures [here]({{ site.baseurl }}{% post_url 2018-06-04-Interview-prep-intro:-Data-structures-and-complexity %}).


## Search
+ Binary search
  + Applied on a sorted array
  + Recursive or iterative implementations
  + If there are more than one matching results (i.e. numbers are not distinct) returns a random index. To find the first one go left.
  + Examples: 9.3 in [1]
+ Generic Search


## Sorting
[Classification](https://en.wikipedia.org/wiki/Sorting_algorithm#Classification)
- Stable: the relative order of equal elements is preserved.
- In-place: returns the input sorted (not a copy of it), in practice this means it requires O(1 or logn) auxiliary space.
- Comparative: based on comparisons
  - Comparative algorithms can't do better than nlogn on average or worst cases.
  - All of the following are comparative except counting-radix-bucket.
- General method: insertion, exchange, selection, merging, etc.
- Adaptability: Whether or not the presorted-ness of the input affects the running time.

| Name | Time (best-worst) | Space | Stable? | In-place |
|---|-|-|-|-|
| Insertion | n-n^2 | O(1) | yes | yes |
| Selection | n^2 | O(1) | [no](https://stackoverflow.com/questions/20761396/why-selection-sort-can-be-stable-or-unstable) | yes |
| Heapsort | nlogk-nlogn | O(1) | no | yes |
| Mergesort | nlogn | O(n) | yes | no |
| Quicksort | nlogn-n^2 | O(logn) | no (usually) |  
| Counting sort | n | k | - (doesn't make sense for ints)| yes |  


### Details
- Insertion: splits sorted and unsorted part of the input, [implementation](https://www.geeksforgeeks.org/insertion-sort/) looks back in the sorted part for every element. Can be used as the base case for other divide n conquer algos (merge, quicksort or bucket sort)
- Selection: [Implementation](https://www.geeksforgeeks.org/selection-sort/) looks ahead for the next minimum
  - Heapsort: selection sort with a (max or min) heap
- Quicksort: divide n conquer, [partition method](https://en.wikipedia.org/wiki/Sorting_algorithm#Comparison_of_algorithms). [best theoretical explanation here](https://www.youtube.com/watch?v=ZHVk2blR45Q), worst case when the pivot divides in very small and very big lists (recurrence T(n) = T(n-1) + O(n) and quadratic time).
  - Found 2 similar implementations (more [here](https://en.wikibooks.org/wiki/Algorithm_Implementation/Sorting/Quicksort))
  1. Choose pivot, swap it to the end (or just choose last), keep an index of first larger than pivot element and run through the elements swaping with a smaller element. At the end swap the pivot with the larger element. Repeat for left and right side e.g. [here](https://www.youtube.com/watch?v=ZHVk2blR45Q) or [3]
  2. Choose pivot, swap left with right elements until the pointers cross, repeat for left and right list, see [1]
  - Introsort: Quicksort + Heapsort
  - Practical example: 18.6 in [1]
- Mergesort: divide 'n' conquer, merging method.
  - Timsort: mergesort + insertion
- Counting[3]
  - Bucket[3]: uniform distribution and ordered hash, hash + insertion sort in the bucket.
  - [Radix](https://www.geeksforgeeks.org/radix-sort/): get max, counting sort per digit


### Use-cases
- Small n or almost sorted? insertion (worst case on reversely sorted array)
- Guaranteed nlogn worst case or [k-sorted array](https://www.geeksforgeeks.org/nearly-sorted-algorithm/)? heapsort
- Stability is a must, we sort a [linked list](https://www.geeksforgeeks.org/merge-sort-for-linked-list/) (or other slow random access data) or data does not fit in main memory: [mergesort](https://www.toptal.com/developers/sorting-algorithms/merge-sort)
- Fastest average case: quicksort
- Reverse input: shell sort or heap?
- Values within a range (possibly with many duplicates): counting sort. Range too big? Radix or bucket sort (provided uniform distribution of input)
- Want to minimise swaps? [Selection](https://www.toptal.com/developers/sorting-algorithms/selection-sort)

[Cool visualisation](https://www.toptal.com/developers/sorting-algorithms)


### Java implementation details
- Quicksort needs logn space.
