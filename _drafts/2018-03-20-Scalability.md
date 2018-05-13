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

See cahces post about caches.

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
+ [Scalability Rules](http://scalabilityrules.com/)
