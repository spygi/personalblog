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
+ Atomicity:
+ Optimistic vs pessimistic locking:  
  + Optimistic: compare and swap, do the change and then amend if possible or discard
  + Pessimistic: lock first, then change (synchronised blocks etc)
+ Types of locks: intrinsic/implicit vs explicit
  + Based on how the lock object is specified
+ [Semaphore vs mutexes](https://en.wikipedia.org/wiki/Semaphore_(programming)#Semaphores\_vs.\_mutexes): mutex is a binary semaphore (locked or unlocked) that can be accessed only by the thread that locked it.
+ [Context switch](https://en.wikipedia.org/wiki/Context_switch): is the process of storing the state of a process or of a thread, so that it can be restored and execution resumed from the same point later.


## Thread issues
+ Race conditions or [thread interference](https://docs.oracle.com/javase/tutorial/essential/concurrency/interfere.html): Interference happens when two operations, running in different threads, but acting on the same data, interleave. This means that the two operations consist of multiple steps, and the sequences of steps overlap.
+ [Memory consistency](https://docs.oracle.com/javase/tutorial/essential/concurrency/memconsist.html): Memory consistency errors occur when different threads have inconsistent views of what should be the same data. Has to do with visibility.
+ Deadlock: 4 conditions (mutual exclusion, hold and wait, no preemption, circular wait)(CtCi)
+ Livelock: a processor keeps retrying an operation that fails (EPI)
+ Starvation: a processor needs a resource but never gets it (EPI)


## Java specific
Running tasks simultaneously:
+ by extending `Thread` or implementing `Runnable` and passing it to a `Thread` constructor
  + The latter is usually preferred because you can still implement other interfaces and you don't pass the full overhead of Thread class
+ by implementing `Callable` and `submitting` it to an `ExecutorService` (which is obtained from `Executors`)
  + [Callable is "better" than runnable because it returns a value and can throw an exception](https://www.javaworld.com/article/2078679/java-concurrency/java-concurrency-modern-threading-for-not-quite-beginners.html)
+ [ForkJoinPool](https://www.ibm.com/developerworks/library/j-jtp11137/index.html)  
  + [Best suited for parallelism, map reduce, indepent tasks](https://stackoverflow.com/questions/21156599/javas-fork-join-vs-executorservice-when-to-use-which)
  + Uses ExecutorService underneath anyway
+ Parallel Streams


### Thread safety on variables
+ `volatile` variables [source](http://www.javamex.com/tutorials/synchronization_volatile.shtml): The value of this variable will never be cached thread-locally: all reads and writes will go straight to "main memory"; : Access to the variable acts as though it is enclosed in a synchronized block, synchronized on itself. [Note 1](https://stackoverflow.com/a/3038233/2259743): volatile ensures atomicity of double & longs writes and reads even though they (without volatile) happen in 2 operations. Note 2: `myVolatile++;` is a compound action therefore not thread safe.  
  + [Static volatile variables make sense](https://stackoverflow.com/questions/2423622/volatile-vs-static-in-java)
+ AtomicReferenceFieldUpdater->VarHandles (in Java 9)
+ java.util.concurrent.atomic for AtomicInteger etc.
+ ThreadLocal is in a sense the opposite of volatile: each thread maintains a local


### Locking (on code)
1. Synchronization with `synchronized`
  + It's an intrinsic (or implicit or monitor lock), re-entrant lock: the thread that already has the lock can re-enter the synchronized code (but not another thread).
  + Can be on a method (static or instance) or block of code. Difference?
    + Method ones have [more overhead](https://docs.oracle.com/javase/tutorial/essential/concurrency/syncmeth.html) blocks all other synchronized methods: if it's on an instance method blocks all synchornized instance methods, if on static blocks all static ones. This ensures the visibility property: when a synchronized method exits, it automatically establishes a happens-before relationship with any subsequent invocation of a synchronized method for the same object. [Another resource](http://www.javapractices.com/topic/TopicAction.do?Id=35)
2. Locks (e.g. mutex, semaphore) `Lock`, `Semaphore`
  + Explicit locks, provide more granular control and semantics.
  + Can be re-entrant or not and support various `Condition`s.
  + Can just attempt a lock: [`tryLock`](https://docs.oracle.com/javase/tutorial/essential/concurrency/newlocks.html)

Locking guarantees both visibility and atomicity properties.


## Resources
+ [Oracle](https://docs.oracle.com/javase/tutorial/essential/concurrency/index.html)
+ [IBM developer works](http://www.ibm.com/developerworks/library/j-5things15/)
