---
layout: post
date:   2018-06-18
categories: software-engineering
tags: self-study concurrency Java
---
A quick reference on concurrency and related concepts with Java specifics.

## Concepts
+ Concurrency vs parallelism:
  + Running many different things at the same time vs improving the performance of one thing
  + [One runs with -potential-interruptions while the other runs independently](https://stackoverflow.com/a/24684037/2259743)
  + [Can run in overlapping time periods (multitasking in single core) vs literally ran simultaneously (multicores)](https://stackoverflow.com/a/1050257/2259743)
+ [Threads vs process]((https://msdn.microsoft.com/en-us/library/windows/desktop/ms681917%28v=vs.85%29.aspx?f=255&MSPPError=-2147217396)) (should be true for Unix too)
  + Lightweight, share address space vs heavyweight, do not share memory
+ Visibility: a change is visible in all threads
  + The memory model specifies when and how changes become visible[Effective]
+ [Atomicity](http://preshing.com/20130618/atomic-vs-non-atomic-operations/): An operation acting on shared memory is atomic if it completes in a single step relative to other threads. When an atomic store is performed on a shared variable, no other thread can observe the modification half-complete. When an atomic load is performed on a shared variable, it reads the entire value as it appeared at a single moment in time. Non-atomic loads and stores do not make those guarantees.
  + In Java reads/writes to variable are atomic unless the variables are long or double. Use `AtomicLong` instead.

+ Optimistic vs pessimistic locking:  
  + Optimistic: compare and swap, do the change and then amend if possible or discard
  + Pessimistic: lock first, then change (synchronised blocks etc)
+ Types of locks: intrinsic/implicit vs explicit
  + Based on how the lock object is specified
+ [Semaphore vs mutexes](https://en.wikipedia.org/wiki/Semaphore_(programming)#Semaphores\_vs.\_mutexes): mutex is a binary semaphore (locked or unlocked) that can be accessed only by the thread that locked it.
+ [Monitor vs Semaphore](https://stackoverflow.com/a/7336516): monitor is like a public toilet, semaphore like a bike hire place.
+ [Context switch](https://en.wikipedia.org/wiki/Context_switch): is the process of storing the state of a process or of a thread, so that it can be restored and execution resumed from the same point later.


## Thread issues
From [Epi]:
+ Race conditions or [thread interference](https://docs.oracle.com/javase/tutorial/essential/concurrency/interfere.html): Interference happens when two operations, running in different threads, but acting on the same data, interleave. This means that the two operations consist of multiple steps, and the sequences of steps overlap.
+ [Memory consistency](https://docs.oracle.com/javase/tutorial/essential/concurrency/memconsist.html): Memory consistency errors occur when different threads have inconsistent views of what should be the same data.
  + Has to do with visibility.
  + Mutability of data
+ Deadlock: when a thread is waiting a lock another thread has, while the other thread waits for a lock the first one has.
  + 4 conditions should be met (mutual exclusion, hold and wait, no preemption, circular wait)[Ctci]
+ Livelock (liveness failure in [Effective]): a processor keeps retrying an operation that fails
+ Starvation: a processor needs a resource but never gets it


## Java specific
Running tasks simultaneously:
+ by ~~extending `Thread` or~~ implementing `Runnable` and passing it to a `Thread` constructor
  + The latter is preferred because you can still implement other interfaces and you don't pass the full overhead of Thread class[Ctci]
+ by implementing `Callable` and `submitting` it to an `ExecutorService` (which is obtained from `Executors`)
  + [Callable is "better" than Runnable because it returns a value and can throw an exception](https://www.javaworld.com/article/2078679/java-concurrency/java-concurrency-modern-threading-for-not-quite-beginners.html)
+ [ForkJoinPool](https://www.ibm.com/developerworks/library/j-jtp11137/index.html)  
  + [Best suited for parallelism, map reduce, indepent tasks](https://stackoverflow.com/questions/21156599/javas-fork-join-vs-executorservice-when-to-use-which)
  + Uses ExecutorService underneath anyway
+ Parallel Streams


### Thread safety on variables
+ `volatile` variables: The value of the variable will never be cached thread-locally: all reads and writes will go straight to "main memory". **Access** to the variable acts as though it is enclosed in a synchronized block, synchronized on itself.[source](http://www.javamex.com/tutorials/synchronization_volatile.shtml)
  + [Note 1](https://stackoverflow.com/a/3038233/2259743): volatile ensures atomicity of double & longs writes and reads even though they (without volatile) happen in 2 operations.
  + Note 2: `myVolatile++;` is a compound action (get and set) therefore not thread safe using just volatile!
  + [Static volatile variables make sense](https://stackoverflow.com/questions/2423622/volatile-vs-static-in-java)
+ AtomicReferenceFieldUpdater->VarHandles (in Java 9)
+ java.util.concurrent.atomic for AtomicInteger etc.
+ ThreadLocal is in a sense the opposite of volatile: each thread maintains a local


### Locking (on code)
Locking in general guarantees both visibility and atomicity properties.

1. Synchronization with `synchronized`
  + It's an intrinsic (or implicit or monitor) lock
  + It's **re-entrant**: the thread that already has the lock can re-enter the synchronized code (but not another thread).
  + Can be on a method (static or instance) or block of code. Difference?
    + Method ones have [more overhead](https://docs.oracle.com/javase/tutorial/essential/concurrency/syncmeth.html): they block all other synchronized methods i.e. if it's on an **instance** method all synchronized instance methods are blocked, if on a **static** all static ones are blocked. This ensures the visibility property: when a synchronized method exits, it automatically establishes a happens-before relationship with any subsequent invocation of a synchronized method for the same object. [Another resource](http://www.javapractices.com/topic/TopicAction.do?Id=35)
2. Locks (e.g. mutex, semaphore: counting/binary) `Lock`, `Semaphore`
  + Explicit locks, provide more granular control and semantics.
  + Can be re-entrant or not and support various `Condition`s.
  + Can just attempt a lock: [`tryLock`](https://docs.oracle.com/javase/tutorial/essential/concurrency/newlocks.html)


## Resources
[Ctci]: https://www.amazon.com/Cracking-Coding-Interview-Programming-Questions/dp/098478280X "Cracking the coding interview (5th edition), Chapter 16"
[Epi]: https://www.amazon.com/Elements-Programming-Interviews-Java-Insiders/dp/1517671272 "Elements of Programming Interviews (in Java), Chapter 19"
[Effective]: https://www.amazon.com/Effective-Java-2nd-Joshua-Bloch/dp/0321356683 "Effective Java (2nd edition), Chapter 10"

+ [Oracle](https://docs.oracle.com/javase/tutorial/essential/concurrency/index.html)
+ [IBM developer works](http://www.ibm.com/developerworks/library/j-5things15/)
+ [Effective Java][Effective]
+ [Cracking the coding interview][Ctci]
+ [Elements of Programming Interviews in Java][Epi]
