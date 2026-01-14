- [URL shortening service](#url-shortening-service)
- [Functional Requirements](#functional-requirements)
- [Non-Functional Requirements](#non-functional-requirements)
- [Core Entities](#core-entities)
- [API](#api)
  - [POST](#post)
  - [GET](#get)
- [High Level Design](#high-level-design)
  - [Core Components:](#core-components)
  - [URL Shortening Flow](#url-shortening-flow)
  - [Redirect Flow for Shortened URL](#redirect-flow-for-shortened-url)
- [Deep Dives](#deep-dives)
  - [Unique short URLs](#unique-short-urls)
  - [Generating Unique Short URLs with Hash Functions](#generating-unique-short-urls-with-hash-functions)
  - [Fast Redirects](#fast-redirects)
  - [Fast Lookup via Indexing](#fast-lookup-via-indexing)
  - [In-Memory Cache for Fast Redirects](#in-memory-cache-for-fast-redirects)
  - [Using CDN \& Edge for Low-Latency Redirects](#using-cdn--edge-for-low-latency-redirects)
  - [Support Writes](#support-writes)
  - [Database Technology:](#database-technology)
  - [High Availability Strategies:](#high-availability-strategies)
  - [Primary Server scaling](#primary-server-scaling)
  - [DB Table schema](#db-table-schema)

#  URL shortening service

Design a highly available and scalable URL shortening service that converts long URLs into unique short links and redirects users to the original URLs with low latency. The system should support optional custom aliases and expiration times, handle a read-heavy workload at massive scale, and prioritize availability while maintaining global uniqueness of short URLs.

# Functional Requirements

- Generate a shortened URL from a given long URL
- Allow optional custom alias for the shortened URL
- Allow optional expiration time for a shortened URL
- Redirect users from the shortened URL to the original URL

Out of scope:
- User accounts / authentication
- Analytics (click counts, geo, etc.)

# Non-Functional Requirements

- Short codes must be globally unique
- URL redirection latency < 100ms
- High availability system (≈ 99.99%), favor availability over strong consistency
- Scalable to ~1B shortened URLs and ~100M daily active users

Workload characteristics:
- Read-heavy system (e.g., ~1000 reads per 1 write)
- Design optimized for fast reads (caching, replication)

Out of scope:
- Real-time analytics consistency
- Advanced security (spam / malicious URL detection)

# Core Entities

- Original URL: The original long URL that the user wants to shorten.
- Short URL: The shortened URL that the user receives and can share.
- User: The creator/owner of the shortened URL (optional if anonymous usage is allowed)

# API

Use a REST API and focus on choosing the right HTTP method or verb to use.
- POST: Create a new resource
- GET: Read an existing resource
- PUT: Update an existing resource
- DELETE: Delete an existing resource

## POST

- To shorten a URL, a POST endpoint takes in the long URL
- Optionally a custom alias and expiration date
- Returns the shortened URL.

```json
POST /urls
{
  "long_url": "https://www.example.com/some/very/long/url",
  "custom_alias": "optional_custom_alias",
  "expiration_date": "optional_expiration_date"
}
->
{
  "short_url": "http://short.ly/abc123"
}
```

## GET

- GET endpoint that takes in the short code
- Redirects the user to the original long URL.

```json
// Redirect to Original URL
GET /{short_code}
-> HTTP 302 Redirect to the original long URL
```

# High Level Design

## Core Components:
- Client: Web or mobile app used by the user to create or access short URLs
- Primary Server: Handles business logic, URL creation, validation, and redirection
- Database: Stores mappings of short codes → long URLs, custom aliases, and expiration dates

## URL Shortening Flow
1. Client → Server: User submits a long URL (with optional custom alias and expiration) via POST /urls.
2. Server Validation:
   - Check if the URL is valid
   - Ensure the URL or custom alias doesn’t already exist
3. Short URL Generation:
   - Generate a unique short code or use the validated custom alias
4. Database Insert: Store short code → long URL mapping along with expiration
5. Response → Client: Return the shortened URL

WRITE FLOW:
       1) Generate short URL
       2) Save to DB
      ┌────────────────┐          ┌────────────────┐          ┌──────────────┐
      │                │          │                │          │              │
      │     Client     │ <──────> │ Primary Server │ <──────> │   Database   │
      │                │          │                │          │              │
      └───────┬────────┘          └────────────────┘          └──────┬───────┘
              │                                                      │
              │   POST /urls                                         │
              └──────────────────────────────────────────────────────┘
                                                                     │
                                                       Table: Urls   │
                                                      ───────────────┘
                                                       - short_url_code
                                                       - original_url
                                                       - creation_time
                                                       - expiration_time?
                                                       - created_by

## Redirect Flow for Shortened URL
1. Client → Server: User accesses short URL (GET /abc123).
2. Server Lookup:
- Primary Server queries the database for the short code
- Checks if the code exists and hasn’t expired
3. Redirect Response:
- If valid, server responds with a 302 Temporary Redirect to the original long URL
- Browser follows redirect automatically
4. Why 302 is preferred:
- Allows updates or expiration of short URLs
- Prevents caching by browsers
- Enables click tracking (optional/out of scope)

WRITE: 1) generate short url
       2) save to DB
READ:  1) look up original url in DB
       2) return with 302 redirect
      ┌──────────┐            ┌────────────────┐            ┌────────────┐
      │          │            │                │            │            │
      │  Client  │ <────────> │ Primary Server │ <────────> │  Database  │
      │          │            │                │            │            │
      └────┬─────┘            └────────────────┘            └─────┬──────┘
           │                                                      │
           │  POST /urls                                          │
           │  GET /{short_code}                                   │
           └──────────────────────────────────────────────────────┘
                                                                  │
                                                     Urls Table:  │
                                                   ───────────────┘
                                                    - short url code
                                                    - original url
                                                    - creationTime
                                                    - expirationTime?
                                                    - createdBy

# Deep Dives

## Unique short URLs

Requirements:
- Short codes are unique
- As short as possible
- Codes are efficiently generated

## Generating Unique Short URLs with Hash Functions

1. Why we need entropy
- Short codes must be **unique** and ideally **short**.  
- **Entropy** (randomness) helps prevent collisions when generating codes.

2. Random number approach
- Generate a random number each time a URL is shortened.
- **Pros:** Simple to implement  
- **Cons:** Doesn’t guarantee uniqueness; collisions possible at scale

3. Hash function approach
- Use a **hash function** (e.g., SHA-256) to turn the URL into a fixed-size string.  
- Deterministic: same URL → same hash  
- Can add a **salt/nonce** to:
  - Allow multiple codes per URL  
  - Prevent predictability / guessability  

4. Encoding
- Convert hash output to **base62** (`a–z, A–Z, 0–9`) to make it URL-friendly  
- Take the first **N characters** as the short code  
- **Why base62:**
  - Compact (62 chars instead of 64)  
  - Avoids `+` and `/` which are problematic in URLs  

5. Collision handling
- Even with hashing, collisions can happen as more URLs are stored  
- Collision probability ≈ `n / |S|` (n = codes used, |S| = total code space)  
- **Tradeoff:** longer codes → fewer collisions, shorter codes → user-friendly  
- **Strategy:**
  - Enforce **UNIQUE constraint** in DB  
  - Retry code generation a few times if collision occurs (add random salt)  

6. Example Pseudocode

```java
input_url = "https://www.example.com/some/very/long/url"
canonical_url = canonicalize(input_url)        # normalize URL
hash_code = hash_function(canonical_url)       # e.g., SHA-256
short_code_encoded = base62_encode(hash_code)  # base62 string
short_code = short_code_encoded[:8]            # first 8 chars
```

## Fast Redirects

Full database scans to find a short URL → slow as URL count grows (millions/billions).

Requirement:
- Ensure redirects are fast, ideally < 100ms, for a smooth user experience.
- Need efficient lookup mechanisms (indexes, caching, or in-memory stores) instead of scanning the whole database.

## Fast Lookup via Indexing

Use database indexes on the short_code column to speed up lookups.

Lookup time drops from scanning millions of rows → instant retrieval of the original URL

Approaches:
- B-tree index: Default in most relational DBs; lookup O(log n)
- Primary key: Make short_code the primary key → automatic index + uniqueness enforcement
- Hash index: Some DBs (e.g., PostgreSQL) support hash indexing → O(1) exact-match lookups

Challenges:
- High read volume can overwhelm a disk-based database, even with SSDs and indexing.
Example workload:
100M daily active users × 5 redirects/day = ~500M redirects/day
≈ 5,800 redirects/sec on average
Peak traffic may require ~600k reads/sec
- Single DB instance may struggle under load
- Could increase response times, timeouts, or impact URL creation operations

Need read-optimized strategies beyond just indexing (caching, replication, distributed DBs).

## In-Memory Cache for Fast Redirects

Introduce in-memory cache (e.g., Redis, Memcached) between server and database

On redirect request:
- Check cache → cache hit: return long URL instantly
- Cache miss → query database, then populate cache for future requests

Why it’s fast:

Storage	    Access time 	Notes
Memory	    ~100 ns	        Millions of reads/sec
SSD	        ~0.1 ms	        ~100,000 IOPS
HDD	        ~10 ms	        ~100–200 IOPS

Memory access is 1,000–100,000× faster than disk

Challenges / Considerations:
- Cache invalidation: tricky if URLs are updated/deleted (less critical here since mostly read-only)
- Cache warm-up: initial requests still hit DB
- Memory limits: decide cache size, eviction policy (e.g., LRU), and which entries to store
- Added complexity: tradeoffs between speed, memory, and system design

## Using CDN & Edge for Low-Latency Redirects

- Serve short URLs through a CDN with geographically distributed PoPs
- Cache popular short code → long URL mappings at the edge
- Optionally deploy redirect logic to the edge (e.g., Cloudflare Workers, AWS Lambda@Edge)
  - Requests handled close to the user
  - Often bypasses Primary Server entirely → extremely low latency

Benefits:
- Reduces latency for global users
- Offloads traffic from origin server
- Fast redirects for frequently used short URLs

Challenges / Considerations:
- Cache invalidation & consistency across nodes
- Edge function limits: execution time, memory, libraries
- Higher cost for high traffic
- Complex debugging & monitoring in distributed environment

## Support Writes

 Scale to support 1B shortened urls and 100M DAU

Database Size Estimate per Row

Field sizes:
- short code: 8 bytes
- long URL: ~100 bytes
- creationTime: 8 bytes
- optional custom alias: ~100 bytes
- expiration date: 8 bytes
- metadata overhead: ~200–500 bytes

Implication:
- 1B rows × 500 bytes ≈ 500 GB of storage

## Database Technology:
- Any reasonable DB works: Postgres, MySQL, DynamoDB, etc.
- Writes are low (~100k new URLs/day → ~1 row/sec)
- Offloaded reads to cache → heavy read throughput handled

## High Availability Strategies:
1. Replication: multiple DB copies on different servers → failover if one goes down
2. Backup: periodic snapshots stored separately → recover in case of failure

Both add operational complexity, replication preferred for live failover.

## Primary Server scaling

Read/Write Separation:
- Read-heavy workload → separate services:
  - Read Service: handles redirects
  - Write Service: handles short URL creation
- Allows independent scaling of reads and writes

Horizontal Scaling:
- Add multiple instances of each service to distribute load
- Requests routed randomly to service instances
- Handles large number of requests per second

Global Counter for Short Codes:
- Write Service instances need a single source of truth for counter to ensure unique short codes
- Solution: centralized Redis
  - Stores counter & shared metadata
  - Fast, single-threaded, supports atomic increment
- Workflow:
  1. Write Service gets next counter value from Redis
  2. Computes short code
  3. Stores mapping in database

Network overhead:
- Extra network request to Redis per write is usually negligible  
- Can be optimized using counter batching

Counter Batching:
1. Write Service requests a batch of counter values (e.g., 1000) from Redis
2. Redis atomically increments the counter by batch size and returns the start
3. Write Service uses the batch locally for new short codes
4. When batch is exhausted → request new batch

Benefits:
- Reduces load on Redis
- Fewer network calls
- Maintains uniqueness across instances

High Availability:
- Use Redis replication and failover (e.g., Redis Enterprise)
- Optionally persist counter to durable storage periodically


                                 ┌────────────────┐
                                 │ Global Counter │
                                 └───────┬────────┘
                                         │
                                  get latest count
                                         │
                                 ┌───────▼────────┐          ┌──────────────┐
                          ┌─────►│ Write Service  ├─────────►│              │
                          │      │ - generate url │          │              │
      ┌────────┐   ┌──────┴─────┐│ - save to DB   │          │              │
      │        │   │            │└────────────────┘          │   Database   │
      │ Client │<──►API Gateway │                            │              │
      │        │   │ (Routing)  │┌────────────────┐          │              │
      └────────┘   └──────┬─────┘│  Read Service  ├─────────►│              │
                          │      │ - DB lookup    │          └──────┬───────┘
                          └─────►│ - 302 redirect │                 │
                                 └───────┬────────┘          ┌──────▼───────┐
                                         │                   │  Urls Table  │
                                         │                   ├──────────────┤
                                 ┌───────▼────────┐          │- short_code  │
                                 │ Cache (Redis)  │          │- original_url│
                                 │ key: short_code│          │- creationTime│
                                 │ val: orig_url  │          │- expiration? │
                                 └────────────────┘          │- createdBy   │


## DB Table schema

urls_table

| Column          | Type        | Notes                                         |
|-----------------|------------|-----------------------------------------------|
| short_code      | VARCHAR    | Primary key, unique short code                |
| original_url    | TEXT       | The original long URL                         |
| creation_time   | BIGINT     | Unix timestamp (64-bit)                       |
| expiration_time | BIGINT     | Optional, Unix timestamp for expiration       |
| custom_alias    | VARCHAR    | Optional, user-specified alias                |
| metadata        | JSON/BLOB  | Optional, e.g., creator_id, analytics_id     |

```sql
CREATE TABLE urls (
    short_code      VARCHAR(20) PRIMARY KEY,  -- unique short code
    original_url    TEXT NOT NULL,          -- original long URL
    creation_time   BIGINT NOT NULL,        -- Unix timestamp
    expiration_time BIGINT,                 -- optional Unix timestamp
    custom_alias    VARCHAR(100),           -- optional user-specified alias
    metadata        JSONB                   -- optional metadata (creator_id, analytics)
);

-- 1️⃣ Insert a new short URL (POST)
INSERT INTO urls (short_code, original_url, creation_time, expiration_time, custom_alias, metadata)
VALUES ('abc123', 'https://www.example.com/some/long/url', EXTRACT(EPOCH FROM NOW())::BIGINT,
        EXTRACT(EPOCH FROM NOW() + INTERVAL '30 days')::BIGINT,
        'my-custom-alias',
        '{"creator_id": 1}'::JSONB)
ON CONFLICT (short_code) DO NOTHING;  -- avoid duplicate short_code

-- 2️⃣ Retrieve original URL for redirect (GET)
SELECT original_url
FROM urls
WHERE short_code = 'abc123'
  AND (expiration_time IS NULL OR expiration_time > EXTRACT(EPOCH FROM NOW())::BIGINT);

```

