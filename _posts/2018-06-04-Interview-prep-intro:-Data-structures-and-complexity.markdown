---
permalink: tech-interview-intro
layout: post
categories: theoretical-cs
tags: self-study java interview-prep
---
[1]: https://www.amazon.com/Cracking-Coding-Interview-Programming-Questions/dp/098478280X "Cracking the coding interview (5th edition)"
[2]: https://www.amazon.com/Elements-Programming-Interviews-Java-Insiders/dp/1517671272/ref=pd_sim_14_3?_encoding=UTF8&pd_rd_i=1517671272&pd_rd_r=XEQKDVEBYF5DAWEC6B7W&pd_rd_w=8RwvQ&pd_rd_wg=L59s8&psc=1&refRID=XEQKDVEBYF5DAWEC6B7W&dpID=51EjkypFE0L&preST=_SY291_BO1,204,203,200_QL40_&dpSrc=detail "Elements of Programming Interviews (in Java)"
[3]: "Algorithms in a Nutshell"
[4]: http://steve-yegge.blogspot.com/2008/03/get-that-job-at-google.html "Get that job at Google"

This is the first part on a quick reference/revision on data structures and algorithms. My main resources are 3 books:
- Cracking the Coding interview<sup>[1]</sup>
- Elements of Programming Interviews in Java ([2])
- Algorithms in a Nutshell

Implementing details will be in Java. Disclaimer: my main goal is interview preparation and not a thorough intro on CS fundamentals, so expect some simplifications or omissions.


## First things first: terminology
+ Time complexity
+ Space complexity: memory needed at *any* point in time. Unlike time complexity this is not the total/cumulative used by the algorithm.
  + We care for *auxiliary space* complexity. This means to ignore input storage (otherwise we would start with O(n) and upwards [2]) [SO question](https://stackoverflow.com/questions/30220305/how-to-calculate-the-space-complexity-of-function)
  + Allocations (e.g. of an array of size n) count against the complexity.
  + Recursion depth matters because variables stay in the stack. Specifically the space used is equal to "the deepest level of recursion" multiplied by "the size of the stack frame for each level". 2 remarks:  
    + The above is not true in languages with tail call optimization (like ES6) because they don't assign a new stack on tail recursion.
    + In the fibonacci algorithm (`return fibonacci(i-1) + fibonaci(i-2);`) the first part is evaluated completely before the second, therefore we never get deeper than n levels of recursion.
+ Cases can be worst, average or best: usually we refer to average or worst case.
+ Analysis can be asymptotic or amortized.  
  + Complexity f(n) is usually expressed in terms of big O-notation (upper bound - takes at most f(n) time/space). Other notations are lower bound Ω, tight-bound Θ (both lower and upper bound - takes at least and at most f(n) time/space) or little-o (stricter than big-O) but they won't be used in an interview setting.
  + For reference, from smaller to bigger - O(1): constant, O(logn): logarithmic, O(sqrt(n)) or generally n^d where 0<d<1: less common, appears in primality check, O(n): linear, O(nlogn): quasilinear, O(n^2): quadratic, O(n^3): cubic, O(n^k): polynomial, O(2^n): exponential, O(n!): factorial
  + Amortized: is the "average" cost over a number of operations because worst case can be too pessimistic, for more see [amortized analysis wiki](https://en.wikipedia.org/wiki/Amortized_analysis). An example in data structures is the cost of resizing an array list: since it happens every n insertions, it's amortized cost is O(1) among all insertions.    

[More](http://www.leda-tutorial.org/en/official/ch02s02s03.html) [resources](http://bigocheatsheet.com/).


## Basic data structures
Mostly from [2]. The basic operations are access (knowing the index), lookup/search, insertion and deletion.

Some notes:
+ Insertion and deletion complexities are the same (afaik).
+ All of the data structures need O(n) space (duuuh), besides skip list?

|name|access|search|insert/delete|Java|custom impl., notes|
|-   |-     |-     |-            |-           |-          |  
|arrays|O(1)|O(n)|O(n)|Arrays, ArrayList||
|strings|same as array|||String, StringBuilder|immutable (in most?) languages|
|list|O(n)|O(n)|O(1)|LinkedList (is doubly linked!)|array or dynamic allocation|
|stack|array or list, depends on implementation||| ArrayDeque or LinkedList (not Stack interface) | array or dynamic, LIFO, think of a stack of plates |
|queue|same as stack||| ArrayDeque or LinkedList | FIFO, array or dynamic |
|tree|depends on array or list implementation| |||single or double list to keep reference to parent|
|heap| ? | O(1) for max|O(logn)| PriorityQueue |pointer or 1-dimensional array representation|
|BST| ? |O(h=logn) for balanced trees|O(h)| TreeSet or TreeMap ||
|hash table| - |O(1)|O(1)| HashSet or HashMap ||
|graph|

More data types: Abstract [ADT](https://en.wikipedia.org/wiki/Abstract_data_type)
+ bag: unordered set, can contain duplicates
+ set: unordered, no duplicates

For related Java classes and implementation tips see [here]({{ site.baseurl }}{% post_url 2018-08-29-Java %}).

### How to choose an appropriate structure
- Do you have the algorithm in mind already?
  - Look at the special properties of each structure e.g. use stack if you need to process elements in LIFO order from the input or a set to remove duplicates.
  - Look at what operations will we do the most e.g.
    - Insertions/deletions at start or middle: a list would be better than an array because an array would need to be shifted. If they are on the end they don't affect that much.
    - Search/lookups? Hashmap if we need to associate it with a value, hashset otherwise
    - Random access (e.g. in a map[][])? Array

If you need to create a custom implementation:
- Initial capacity? What happens on resizing?
- Permits nulls or duplicates?
- Should it be thread safe?


### In real life
+ The call stack aka program stack is really [implemented as a stack](http://www.inf.udec.cl/~leo/teoX.pdf): the last method to enter executes first. Heap like a linked list?
+  
