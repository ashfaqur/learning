# Caching

What Is Caching?

Caching is the technique of storing copies of frequently accessed data in a fast-access storage layer (like RAM) so future requests can be served quickly without hitting slower backend systems (e.g., databases). The primary goal is to reduce latency and offload the origin data source

## Why Use Caching?

Core Benefits

Faster response time — reads from cache are much quicker than database/disk fetches. 

Reduced backend load — fewer repeated database/API calls. 

Improved scalability — system can handle high read traffic. 

Lower cost — reduces infrastructure and compute costs. 

Better user experience — faster responses for users

## Where Caching Can Be Placed

Client-side cache (browser, local storage) — avoids network calls. 

CDNs / Edge caches — static content near users globally. 

In-process cache (local app memory) — fastest but not shared across servers. 

External cache (Redis, Memcached) — shared across instances

## Caching Patterns / Strategies

Cache–Aside (Lazy Loading)

App checks cache → if miss → load from DB, insert into cache.

Best for read-heavy systems.

Read-Through

Cache itself fetches data from DB on a cache miss.

App always talks to cache; simpler app logic. 

Write-Through

Writes go to cache first, then synchronously to DB.

Ensures good consistency, but slower writes. 

Write-Back (Write-Behind)

Writes go to cache first. DB update happens asynchronously.

Fast writes; possible data loss if cache fails. 

## Eviction & Expiration Policies

Caches have limited space, so they remove entries based on policies:

LRU (Least Recently Used) — evict least recently accessed. 

LFU (Least Frequently Used) — evict least frequently accessed. 

FIFO (First In, First Out) — evict oldest inserted. 

TTL (Time-to-Live) — expire entries after a set time.

## Challenges & Trade-offs

Cache Invalidation

Keeping cache and DB in sync is difficult when data changes.

Strategies include TTL, explicit invalidation, or writing through caches. 

Stale Data

Cached data may become outdated (consistency issues). 

Cold Cache

After restarts or expirations, the cache is empty → high load on backend. 

Cache Stampede

Many clients miss simultaneously → sudden DB load spike.

## Best Practices

Set appropriate TTL for cache keys. 

Invalidate/refresh cache on writes when strong consistency is required. 

Monitor hit/miss ratios and performance. 

Use distributed cache for shared and scalable use cases (e.g., Redis Cluster).
