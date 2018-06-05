---
layout: post
date:   2018-06-04
categories: theoretical-cs
tags: self-study
---
[1]: https://www.amazon.com/Cracking-Coding-Interview-Programming-Questions/dp/098478280X "Cracking the coding interview (5th edition)"
[2]: https://www.amazon.com/Elements-Programming-Interviews-Java-Insiders/dp/1517671272/ref=pd_sim_14_3?_encoding=UTF8&pd_rd_i=1517671272&pd_rd_r=XEQKDVEBYF5DAWEC6B7W&pd_rd_w=8RwvQ&pd_rd_wg=L59s8&psc=1&refRID=XEQKDVEBYF5DAWEC6B7W&dpID=51EjkypFE0L&preST=_SY291_BO1,204,203,200_QL40_&dpSrc=detail "Elements of Programming Interviews (in Java)"

This is the first part on a quick reference/revision on data structures and algorithms. My main resources are 3 books:
- Cracking the Coding interview ([1]) and the accompanying repo
- Elements of Programming Interviews in Java ([2]) and the repo
- Algorithms in a Nutshell

Implementing classes will be in Java. Disclaimer: my main goal is interview preparation and not a thorough intro on CS fundamentals, so expect some simplifications or omissions.


## First things first: terminology
+ Time complexity: is analysed in depth in every resource.
+ Space complexity: memory needed at *any* point in time. Unlike time complexity this is not the total/cumulative used by the algorithm.
  + Usually we care for *auxiliary space* complexity which means to ignore input storage (otherwise we would start with O(n) and upwards [2]) [SO question](https://stackoverflow.com/questions/30220305/how-to-calculate-the-space-complexity-of-function)
  + Allocations (e.g. of an array of size n) count against the complexity.
  + Recursion depth matters because variables stay in the stack. Specifically the space used is equal to "the deepest level of recursion" multiplied by "the size of the stack frame for each level". Note that for example in the fibonacci algorithm (`return fibonacci(i-1) + fibonaci(i-2);`) the first part is evaluated completely before the second, therefore we never get deeper than n levels of recursion.
+ Cases can be worst, average or best: usually we refer to average or worst case.
+ Analysis can be asymptotic or amortized.  
  + Complexity f(n) is usually expressed in terms of big O-notation (upper bound - takes at most f(n) time/space). Other notations are lower bound Ω, tight-bound Θ (both lower and upper bound - takes at least and at most f(n) time/space) or little-o (stricter than big-O) but they won't be used in an interview setting.
  + For reference, from smaller to bigger - O(1): constant, O(logn): logarithmic, O(sqrt(n)) or generally n^d where 0<d<1: less common, O(n): linear, O(nlogn): quasilinear, O(n^2): quadratic, O(n^3): cubic, O(n^k): polynomial, O(2^n): exponential, O(n!): factorial
  + Amortized: is the "average" cost over a number of operations because worst case can be too pessimistic, for more see [amortized analysis wiki](https://en.wikipedia.org/wiki/Amortized_analysis). An example in data structures is the cost of resizing an array list: since it happens every n insertions, it's amortized cost is O(1) among all insertions.    

[More](http://www.leda-tutorial.org/en/official/ch02s02s03.html) [resources](http://bigocheatsheet.com/).


## Basic data structures
As presented in [2]. The basic operations are access (you have an index already), lookup/search, insertion and deletion. Insertion and deletion complexities are the same afaik.

|name|access|search|insert/delete|Java|custom impl., notes|
|-   |-     |-     |-            |-           |-          |  
|arrays|O(1)|O(n)|O(n)|Arrays, ArrayList|new int[]{1,2}|
|strings|same as array|||String, StringBuilder/Buffer|immutable in all? languages|
|list|O(n)|O(n)|O(1)|ArrayList, LinkedList (doubly)|single or double?|
|stack|array or list, depends on implementation||| ArrayDeque or LinkedList (not Stack interface) | single list (pointing down/back), LIFO, think of a stack of plates |
|queue|same as stack||| ArrayDeque or LinkedList | FIFO |
|tree|depends on array or list implementation| |||single or double list to keep reference to parent|
|heap| ? | O(1) for max|O(logn)| PriorityQueue, PriorityBlockingQueue ||
|BST| ? |O(h=logn) for balanced trees|O(h)| TreeSet or TreeMap ||
|hash table| - |O(1)|O(1)| HashSet or HashTable ||

More data types: Abstract (ADT)
+ bag: unordered set, can contain duplicates
+ set: unordered, no duplicates
[etc](https://en.wikipedia.org/wiki/Abstract_data_type).


### Implementation details
- ArrayDeque: has head and tail: supports inserts/deletions from both ends. The semantics of the queue/stack (i.e. name of method used determine what is returned in each occasion. For example `push` and `pop` for Stack operate on the head, `add`/`offer` of the Queue work on the tail and `remove`/`poll` on the head. Look for `// *** Queue methods ***` (or Stack) in the source ;-)
- PriorityQueue: min heap by default, needs Comparable elements, methods like a ArrayDeque
- HashSet uses HashMap behind the scenes. Supports a contains method in O(1).
- HashTable: complexity


### How to choose an appropriate structure/implementation
- Structure: look at the special properties of each structure e.g. stack is LIFO or tree represents a hierarchy etc.
  - Look at what operations will we do the most e.g. insertions/deletions (start, middle or end), search, access, existence.
- Initial capacity? What happens on resizing?
- Permits nulls or duplicates?
- Synchronization?
