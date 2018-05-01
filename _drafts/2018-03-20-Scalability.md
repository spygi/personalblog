---
layout: post
title:  "Scalability - WIP"
date:   2018-03-20
comments: true
categories: devops
---
## Questions
+ What is scalability? Would you say scalability about partitioning stuff as much as you need so you can scale up or out?
+ What ball range of numbers are we talking? Think highscalability.com

## Architecture
+ HTTP is stateless. But you probably do need some form of *session stickiness* for example to implement a shopping cart. There are [3 ways for that](https://www.quora.com/What-are-the-most-efficient-ways-to-handle-user-sessions-in-a-scalable-environment/answer/Mark-Ayzenshtat):
  + Client-side: first party cookies which can be encrypted. Downside: size of data you can store. What about if user has disabled cookies?
  + Server-side external: with a data store in memory (memcache, redis) or db (noSQL). Advantage: can store more and more complex user data.
    + Centralized (all machines have the same) or not?
  + Server-side internal: on the application or db servers. Bad for scalability because you tie requests with certain servers, what happens when those servers are down (for deployment etc) or when you add new servers (will they be under-utilised?)?
+ Database Layer: replication (read from slaves, write to master), confirmation (optimistic vs pessimistic locking?), sharding (splitting data), denormalization, performance tuning (db parameters or/and querries)
+ Cache
  + Redis: in memory key-value storage with some [persistence options](https://redis.io/topics/persistence)
  + Memcache: simpler (no persistence, no built-in data structures?)?
  + 2 methods: cache querries or objects
    + Database queries (key is the hashed query). Downside: expiration especially for complex queries that might query from various tables/collections.
    + Objects: easier to reason since the db/code follows this pattern too,  freshness is still an issue.
+ Asynchronous jobs
  + Offline jobs
  + Message broker/queue like Kafka, Rabbitmq, Activemq

### Design data sharding
TODO
In cache/db/ etc.
+ Optimization: temporal or spatial locality of data? on READ misses or WRITE misses with allocate.

## Design a cache
+ What to use as a key? We need easy/fast lookups and updates/clean-up.
+ What is the expiration policy?
+ What is the replacement/eviction/flush policy?  

Some pointers on how to reason:
+ Usage patterns?
  + If we read often, consider time expirations.
  + If we write often, consider content expirations.
+ Data patterns?
  + If items can be big, consider giving them a "lower priority" (implemented as a lower counter) to flush them first.
+ Does our data have temporal/spatial or other locality eg do we need to cache per user or per query (across all users)? This will affect the key used.
+ Size: can it fit in RAM? Of a single machine or distributed? If distributed reconsider all the above :P

### Analysis
+ 2 operations: read/write each with 2 outcomes: hit/miss. Obviously: if it's a WRITE miss it would also have been a READ miss for the same item.  

| Operation | HIT | MISS |
| --- |---| ---|
| READ | is it fresh/valid? return data | write data to cache and return |
| WRITE | write-through or write-back | write allocate or no-write allocate |

Typical caches:
  + READ misses: always write data to cache
  + WRITE hits policies can be write-through or write-back: WRITE hits update the cache immediately but might get delayed on the persistent storage. Subsequent reads (will also be hits) have therefore always valid data.
  + WRITE misses can be write-allocate (fetch on write) or no-write (write around): writes misses will go to the persistent storage anyway the question is if they are gonna get fetched to the cache or not. So subsequent READS might be hit (write-allocate, valid data because it was fetched as well) or miss (write around, will go to the persistent storage that has the valid data).<a href="https://en.wikipedia.org/wiki/Cache_(computing)">Wikipedia</a>

+ Policies:
  + *Expiration policies*: "Old/stale/invalid data" concern READ hits: can happen on caches that cache remote objects (eg. HTTP cache in browsers or CDNs). Expiration policies are used to check this: time (absolute or sliding window) or "cache dependency" (based on the content)
    + [Redis expiration](https://redis.io/commands/expire)
  + [*Cache replacement/eviction policies*](https://en.wikipedia.org/wiki/Cache_replacement_policies) What happens when the cache becomes full? If we have write-back, we need to first write stuff to storage. Then we need to start flushing data. How do we choose?
    + FIFO, LIFO, LRU (least recently used), TLRU (time aware = LRU with time expiration), MRU, least frequently used (counter of usages) etc. Used can mean read or write?
    + The expired policy can affect this as the expired items can be evicted first.
    + [Redis replacement policies](https://redis.io/topics/lru-cache) and a problem with [memory fragmentation](https://www.couyon.net/blog/using-redis-as-a-lru-cache-dont-do-it)

### Web examples
[HTTP Caching](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching):
+ Time based expiration policies: `Cache-Control: max-age` in HTTP requests and responses, `Expires:` in HTTP responses, `expires` field in cookies, TTL in DNS
+ Content-based expiration: filename "cache busting" or `ETag`
+ `no-cache` (but use Etag), `no-store` (privacy), `private` (no caching from CDN), `public` (not needed if you have max-age set)
+ jquery cache true sets correctly If-None-Match: W/"2fe8c49403649a091d39c37460bb963b"
If-Modified-Since https://github.com/jquery/jquery/blob/master/src/ajax.js

## Resources
+ [HighScalability](highscalability.com)
+ [Scalability for dummies parts 1-4](http://www.lecloud.net/tagged/scalability)
+ Cracking the coding interview, Chapter 10
+ [Policy suggestions](https://www.neovolve.com/2008/10/08/cache-expiration-policies/)
