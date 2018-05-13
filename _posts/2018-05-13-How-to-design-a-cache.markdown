---
layout: post
title:  "How to design a cache"
date:   2018-05-13
categories: architecture
---
I started reading up on caches. This is my general introduction if you ever need to design one (or understand better how it works from within).

## Basic design questions
+ What is the lookup key? Do we need easy/fast reads or writes/clean-up?
+ What is the **expiration policy**?
+ What is the **replacement policy** (aka eviction or flush)? Note this is different from the expiration policy.

### Some pointers while answering the questions
+ What are the **usage patterns**? If we read often, consider using time expirations. If we write often, consider content expirations.
+ What are the **data patterns**? If individual items can be big, consider giving them a "lower priority" (implemented as a lower counter) to flush them first.
+ Does the data to be cached have temporal/spatial or other **locality** e.g. do we need to cache per user or per query (across all users)? Think of Y-axis (services/features) or Z-axis (clients/customers) splits according to [Scalability Rules](http://scalabilityrules.com/). This can affect the key used as well as the location of the cache.
+ **Total size** of data: can it fit in memory? Of a single machine or distributed? Start from a single machine and then think how to distribute.

## Theory
There are 2 operations to consider: READ/WRITE each with 2 outcomes: hit/miss. Maybe obvious but: if it's a WRITE miss it would also have been a READ miss for the same item.

| Operation | HIT | MISS |
| READ | is it fresh/valid? return data | write data to cache and return |
| WRITE | write-through or write-back | write-allocate or no-write allocate |

Typical caches:
+ READ misses: **always** writes data to cache
+ WRITE hits policies can be write-through or write-back: WRITE hits will update the cache *anyway* but might get delayed on the persistent storage. Subsequent READs will also be cache hits (provided data is still fresh) will therefore serve valid data.
  + An issue here would be if the delay to write on persistent storage is more that the expiration policy.
+ WRITE misses can be write-allocate (fetch on write) or no-write (write around): WRITE misses will go to the persistent storage *anyway*. The question is if they are gonna get fetched to the cache or not. So subsequent READS might be a cache-hit (in the case of write-allocate, valid data because it was fetched as well) or a cache-miss (in the case of write-around, will go to the persistent storage that has the valid data).

See <a href="https://en.wikipedia.org/wiki/Cache_(computing)">Wikipedia on caches</a>.

## Policies
1. Expiration policy: "Old/stale/invalid data" concern *only* READ hits: can happen on caches that cache remote objects (eg. HTTP cache in browsers or CDNs). Expiration policies are used to check this: time (absolute or sliding window) or "cache dependency" (based on the content). For example see [Redis expiration](https://redis.io/commands/expire)
2. [Cache replacement/eviction policies](https://en.wikipedia.org/wiki/Cache_replacement_policies) answer what happens when the cache becomes full? If we have write-back, we first need to write stuff to storage. Then we need to start flushing data. How do we choose?  
 + FIFO, LIFO, LRU (least recently used), TLRU (time aware = LRU with time expiration), MRU, least frequently used (counter of usages) etc. Used can mean read or write?  
 + There is a correlation between the expiration and eviction policy, for example the expired items can be evicted first.  
 + For example see [Redis replacement policies](https://redis.io/topics/lru-cache) and a problem with [memory fragmentation](https://www.couyon.net/blog/using-redis-as-a-lru-cache-dont-do-it)

## More resources
+ Regarding [Policy suggestions](https://www.neovolve.com/2008/10/08/cache-expiration-policies/)
+ Some pointers regarding distributed or not in Cracking the coding interview, Chapter 10
