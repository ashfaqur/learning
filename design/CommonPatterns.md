- [Pushing Realtime Updates](#pushing-realtime-updates)
  - [Step 1: Choosing How Updates Reach the User (Protocols)](#step-1-choosing-how-updates-reach-the-user-protocols)
    - [Option 1 — HTTP Polling](#option-1--http-polling)
      - [Regular Polling](#regular-polling)
      - [Long Polling](#long-polling)
    - [Option 2 — Server-Sent Events (SSE)](#option-2--server-sent-events-sse)
    - [Option 3 — WebSockets](#option-3--websockets)
    - [Rule of Thumb](#rule-of-thumb)
  - [Step 2: How the Backend Produces \& Distributes Events](#step-2-how-the-backend-produces--distributes-events)
    - [Option A — Pub/Sub (Decoupled, Event-Driven)](#option-a--pubsub-decoupled-event-driven)
    - [Option B — Stateful Realtime Servers (Consistent Hashing)](#option-b--stateful-realtime-servers-consistent-hashing)
  - [Summary](#summary)
- [Managing Long-Running Tasks](#managing-long-running-tasks)
  - [Flow](#flow)
  - [Key Insight](#key-insight)
- [Dealing with Contention](#dealing-with-contention)
  - [Pessimistic Locking (Block Others)](#pessimistic-locking-block-others)
  - [Optimistic Concurrency (Detect Conflicts)](#optimistic-concurrency-detect-conflicts)
  - [Serialize Access via Queue](#serialize-access-via-queue)
  - [When Things Get Distributed — It Gets Harder](#when-things-get-distributed--it-gets-harder)
  - [Why Interviewers Emphasize This Line](#why-interviewers-emphasize-this-line)
- [Scaling Reads](#scaling-reads)
  - [Strategies for Scaling Reads](#strategies-for-scaling-reads)
    - [Optimize Reads in the Database](#optimize-reads-in-the-database)
    - [Read Replicas](#read-replicas)
    - [External Caching](#external-caching)
  - [Common Read Scaling Architecture](#common-read-scaling-architecture)
  - [Key Considerations](#key-considerations)
  - [Mental Model](#mental-model)
- [Scaling Writes](#scaling-writes)
  - [Core Strategies for Scaling Writes](#core-strategies-for-scaling-writes)
    - [Horizontal Sharding (Split Data Across Databases)](#horizontal-sharding-split-data-across-databases)
    - [Vertical Partitioning (Split by Data Type)](#vertical-partitioning-split-by-data-type)
    - [Batching \& Write Aggregation](#batching--write-aggregation)
  - [Handling Write Bursts (Traffic Spikes)](#handling-write-bursts-traffic-spikes)
    - [Write Queues (Buffer Spikes)](#write-queues-buffer-spikes)
    - [Load Shedding (Drop Less-Important Writes)](#load-shedding-drop-less-important-writes)
    - [Prioritized Writes](#prioritized-writes)
  - [Why Write Scaling Is Much Harder Than Read Scaling](#why-write-scaling-is-much-harder-than-read-scaling)
  - [Rules of Thumb (Interview-Level Intuition)](#rules-of-thumb-interview-level-intuition)
- [Handling Large Blobs](#handling-large-blobs)
  - [Direct Upload / Download to Blob Storage](#direct-upload--download-to-blob-storage)
  - [What is a presigned URL?](#what-is-a-presigned-url)
  - [Upload workflow (simplified)](#upload-workflow-simplified)
  - [Download workflow](#download-workflow)
  - [Why NOT store blobs in databases?](#why-not-store-blobs-in-databases)
  - [Real-world challenges \& how they’re handled](#real-world-challenges--how-theyre-handled)
  - [Where lifecycle management comes in](#where-lifecycle-management-comes-in)
  - [This keeps storage costs under control.](#this-keeps-storage-costs-under-control)
  - [Why this pattern works so well](#why-this-pattern-works-so-well)
  - [When this pattern may NOT fit](#when-this-pattern-may-not-fit)
  - [Summary](#summary-1)
- [Multi-Step Processes](#multi-step-processes)
  - [What goes wrong without a workflow pattern?](#what-goes-wrong-without-a-workflow-pattern)
  - [Core idea: Treat workflows as state machines](#core-idea-treat-workflows-as-state-machines)
  - [Why this matters in real systems](#why-this-matters-in-real-systems)
  - [Where compensation fits in](#where-compensation-fits-in)
  - [Key mindset shift](#key-mindset-shift)
  - [Summary](#summary-2)
- [Proximity-Based Services](#proximity-based-services)
  - [Core idea](#core-idea)
  - [Common technologies used](#common-technologies-used)
  - [How geo search actually works (mental model)](#how-geo-search-actually-works-mental-model)
  - [Important real-world constraint](#important-real-world-constraint)
  - [Summary](#summary-3)
- [Pattern Selection — Key Notes](#pattern-selection--key-notes)
  - [The Core Idea — Choose Patterns by Trade-offs](#the-core-idea--choose-patterns-by-trade-offs)
  - [Start Simple — Scale When Needed](#start-simple--scale-when-needed)
  - [Interview Relevance — What Good Candidates Do](#interview-relevance--what-good-candidates-do)


# Pushing Realtime Updates

Some applications only need request → response behavior:
- User submits form
- Server does work
- Server returns response

That’s synchronous communication — nothing happens unless the user asks.

But many systems must send updates to users as events happen, for example:
- Chat messages
- Notifications
- Live dashboards & metrics
- Collaborative docs (Google Docs)
- Multiplayer games
- Order tracking

In these cases, the server needs a way to push data to users without them asking each time.

That’s what realtime update patterns are about.

## Step 1: Choosing How Updates Reach the User (Protocols)

This is about how the browser / client receives updates.

Think of it like choosing a “wire” between client and server.

### Option 1 — HTTP Polling

Client asks the server repeatedly: "Anything new?"

Two common styles:

#### Regular Polling

Client polls every X seconds:
- easy to build
- works everywhere

BUT wastes resources if nothing changes

Good for:
- dashboards refreshing every few seconds
- small apps
- interview designs (MVP)

#### Long Polling
- Client asks the server: “Hold this request open until something happens.”
- Server replies only when there’s an update.
- Then client immediately opens another request.
- This reduces waste.

Used historically by apps like:
- early Facebook chat
- early Slack versions

### Option 2 — Server-Sent Events (SSE)

A one-way streaming connection:
- server → client only
- lightweight
- great for broadcasting updates

Examples:
- stock tickers
- live scores
- logs & monitoring dashboards

Built on top of HTTP — easier infra than WebSockets.

Limitations:
- can’t send messages from client → server over same channel
- not great for complex interactivity

### Option 3 — WebSockets

A full-duplex persistent connection:
- client ↔ server both send messages
- stays open as long as needed

Used for:
- chat apps
- multiplayer games
- collaborative editing (Google Docs)
- real-time whiteboards

More powerful — but also:
- harder to scale
- requires sticky connections or routing strategies

### Rule of Thumb

Start with polling.

If you outgrow it → move to SSE or WebSockets.

This matches real-world architecture evolution.

## Step 2: How the Backend Produces & Distributes Events

Once you know how to deliver updates to users…

You must decide how the backend gets messages to the right client.

Two main models.

### Option A — Pub/Sub (Decoupled, Event-Driven)

Publisher sends message → subscribers receive it.

Flow:

- client connects to gateway
- gateway registers client into room:123
- gateway subscribes to pub/sub topic room:123
- chat service publishes message to that topic
- pub/sub broker routes message to subscribed gateways
- gateway forwards message to connected clients in room

Benefits:
- services don’t know about each other
- scalable
- fault-tolerant

Used in systems like WhatsApp.

### Option B — Stateful Realtime Servers (Consistent Hashing)

Instead of messages floating through Pub/Sub where messages get routed anywhere and servers are interchangeable.

Clients are attached to specific servers.
A specific server owns the state for a specific group of users.

Examples:
- Google Docs editors in same document land on same server
- multiplayer game rooms assigned to specific nodes

How routing works:
- hash(document_id) → choose server
- all updates for that document go there

```java
    server = hash(doc_id) % server_count
```

Why?

Because:
- operations need ordering
- conflict resolution is local
- processing may be heavy

This makes collaboration efficient.

But servers must:
- keep state in memory
- failover safely
- require consistent hash ring or coordinator

More complex — but necessary for certain workloads.

## Summary

Realtime updates force decisions along two axes:

How clients receive updates
- Polling
- SSE
- WebSockets

How backend distributes events
- Pub/Sub (stateless, scalable)
- Stateful sharded servers (ordered, localized processing)

Design tradeoffs depend on:
- interactivity
- scale
- latency needs
- complexity tolerance

# Managing Long-Running Tasks

Some tasks simply take too long to process in a normal request-response cycle:
- Video encoding
- PDF/report generation
- Large data imports/exports
- Machine learning model training
- Bulk email sending

If you try to process these synchronously:
- The HTTP request might time out
- Server resources get blocked
- User experience is slow or frustrating

The solution: decouple request acceptance from task execution.

## Flow

- Client submits task
- Server validates → returns job ID
- Worker consumes queue → executes heavy task
- Worker updates status / result
- Client polls / receives notification

## Key Insight

This pattern decouples acceptance from execution. You get:
- Fast responses
- Scalable processing
- Better fault tolerance
  
but you must manage:
- Job tracking
- Retries and failure handling
- Complexity of distributed queueing

# Dealing with Contention

What “Contention” Means

Contention happens when multiple requests try to modify the same resource at the same time.

Examples:
- Two people try to buy the last concert ticket
- Two users try to book the same hotel room
- Two workers update the same bank balance
- Two bids come in at the same time in an auction


If you don’t handle contention, you get:
- double-bookings
- negative stock
- corrupt balances
- inconsistent data
- angry users

So the challenge is:

How do we ensure only one “winner” operation succeeds?

There are three main classes of solutions.

## Pessimistic Locking (Block Others)

Idea:
“If someone is modifying this record, nobody else can.”

Typical implementations:
- DB row lock (SELECT ... FOR UPDATE)
- Application-level mutex
- File lock

Example — ticket booking:
- Transaction starts
- Lock seat row
- Check availability
- Reserve seat
- Commit
- Everyone else waits.

✔ Prevents conflicts
❌ Can cause slowdowns under load
❌ Deadlocks if used incorrectly

Best for:
- financial transactions
- inventory reservations
- auction bidding

## Optimistic Concurrency (Detect Conflicts)

“Let users try — if two modify the same resource, one fails.”

Nobody blocks — but conflicts are rejected.

How it works:
- each row has a version field or timestamp
- client reads version
- client updates WHERE version = old_version
- if 0 rows modified → conflict → retry

Example SQL:

UPDATE tickets
SET owner = 'userA', version = version + 1
WHERE id = 42 AND version = 5;

If someone else updated it first → your update fails.

✔ Fast
✔ Great for web APIs
✔ Good when collisions are rare

❌ Bad for highly-contended resources

Common in:
- REST APIs with ETag
- event-sourcing systems
- document databases

## Serialize Access via Queue

Instead of allowing concurrent writes…

All operations for the same resource go through a queue.

Example:
- All bids for auction#123 go into a queue
- One worker processes them in order
- Only one can win

This works like:
- Bid1
- Bid2
- Bid3

Worker applies them sequentially.
✔ Zero race conditions
✔ Simple logic
✔ Great when ordering matters

❌ Adds latency
❌ Not suitable for low-latency synchronous actions

Used in:
- payment processing
- auctions
- trading engines
- high-integrity systems

## When Things Get Distributed — It Gets Harder

Inside a single database, life is good:
- transactions
- atomic writes
- isolation
- locks
- durability

As soon as you split data across services or databases…

You lose those guarantees.

Now you may need:
- distributed locks (Redis, Zookeeper, Consul)
- two-phase commit (2PC)
- saga pattern
- idempotent operations
- retries & compensating actions

And these introduce:
- latency
- failure cases
- complexity
- partial-commit scenarios

## Why Interviewers Emphasize This Line

“Be careful before splitting your data into multiple databases.”

Because:

Databases already solve:
- concurrency
- transactions
- isolation
- crash recovery
- locking correctness

Once you move to:
- microservices
- sharded data
- multiple DBs

You now have to re-solve:
- ordering
- consistency
- retries
- compensation
- partial failures
- reconciliation logic

Many candidates jump straight to:

“We’ll shard tickets by region and use Kafka”

Interviewers want to see:

“I’d first attempt to handle contention inside a single transactional database, because databases are very good at conflict control. Only if we outgrow scaling limits would I consider distributed coordination — and then I’d apply queuing, sharding, or locking per-resource.”

# Scaling Reads

Reads are much more frequent than writes.

Example: Instagram
- Posting a photo = 1 write
- Opening feed = dozens of reads for posts, user info, likes, comments
- Typical read-to-write ratio: 10:1 → 100:1+

Databases can handle writes fairly easily, but high-volume reads can overwhelm them quickly.

Reads are like “many mouths eating from the same pantry,” while writes are “adding food to the pantry.”

## Strategies for Scaling Reads

There’s a natural progression of strategies as your read traffic grows:

### Optimize Reads in the Database

- Indexing: create indexes on frequently queried columns

Example: SELECT * FROM posts WHERE user_id = ? → add index on user_id

Denormalization: duplicate some data to avoid expensive joins

Example: store user_name in posts table to avoid joining users every time

Query optimization: avoid SELECT *, fetch only what’s needed

Low-hanging fruit, no additional infrastructure needed

### Read Replicas

Databases like Postgres/MySQL allow replication → master handles writes, replicas handle reads

Benefits:

Spread read load across multiple servers

Increase throughput for read-heavy apps

Caveats:

Replication lag → replicas may be slightly stale

Must route reads to replicas and writes to master

### External Caching

Use in-memory caches (Redis, Memcached) or CDNs for static content

Benefits:

Extremely fast read access

Reduces DB load dramatically

Key issues:

Cache invalidation: keep cache consistent with DB writes

Hot keys: very popular content (e.g., trending post) can overwhelm cache

Solutions: sharding cache, pre-warming, rate-limiting

## Common Read Scaling Architecture

Client
  |
  v
Cache Layer (Redis, CDN)
  |
  v
Read Replicas (Postgres/MySQL)
  |
  v
Primary DB (writes only)


Flow:

Client requests data → check cache first

Cache miss → go to read replica

Replica returns data → optionally update cache

Writes always go to primary DB → propagate to replicas

## Key Considerations

Cache invalidation strategies:
- Time-based (TTL)
- Event-based (update cache when DB changes)

Replication lag:
- Some applications tolerate slightly stale reads (eventually consistent)
- Critical reads may still need master DB

Hot keys / trending content:
- Use separate caching strategy
- Avoid single cache node becoming a bottleneck

## Mental Model

DB optimization → faster queries, no extra servers

Read replicas → horizontal scaling of reads

Cache/CDN → extremely fast, low-latency access, offload DB completely

Think: “Don’t hit the DB if you don’t have to. If you must, let replicas share the load. Optimize queries before adding servers.”

# Scaling Writes

Reads can be scaled by:
- adding replicas
- caching
- CDNs

…but writes must modify the source of truth — which means:

You can’t just duplicate writes everywhere without coordination.

Writes are limited by:
- disk throughput
- transaction cost
- network & replication overhead
- consistency guarantees

At high scale (payments, logs, messaging, analytics), a single DB can’t keep up.

So we need strategies that spread writes across machines.

## Core Strategies for Scaling Writes

There are three main categories.

### Horizontal Sharding (Split Data Across Databases)

Instead of one giant database, you create multiple smaller DBs, each owning a subset of the data.

Example sharding strategies:
- by user id
- by region
- by tenant / organization
- by hash(key)

Example:
Users A–M  → Shard 1
Users N–Z  → Shard 2

Now writes are split across shards:
- 2× shards → 2× write throughput
- 10× shards → 10× throughput

Key challenge: choosing a good partition key

It must:
- spread load evenly
- keep related data together
- avoid hot spots (e.g., one shard overloaded)

Bad key example:

“shard by country” → everyone in India or US hits the same shard → hotspot

Good key example:

hash(user_id) → uniformly distributed

### Vertical Partitioning (Split by Data Type)

Instead of splitting rows, split tables / domains into different databases.

Example:

User profile data         → DB #1
Billing + payments        → DB #2
Logs + analytics events   → DB #3
Messages                  → DB #4


Why this helps:
- different datasets scale independently
- different SLAs and durability needs
- different storage engines may be better suited

For example:
- messages may go to NoSQL
- transactions stay in relational DB
- logs go to column store or object storage

### Batching & Write Aggregation

Writing one row at a time is expensive.

So instead we:
- buffer writes in memory
- group them
- insert in bulk

Examples:
- batch inserts in analytics pipelines
- database COPY or bulk-load operations
- Kafka → worker → batch write to DB

Benefits:
- fewer round trips
- fewer transactions
- dramatically higher throughput

Trade-off:
- Data may arrive with slight delay (milliseconds → seconds)

## Handling Write Bursts (Traffic Spikes)

Sometimes the system receives too many writes at once (Black Friday, viral post, etc.).

Three tools help here.

### Write Queues (Buffer Spikes)

Writes go into a queue first, workers drain queue at safe speed.

Flow:

Client → API → Queue → Worker → DB

Benefits:
- prevents DB overload
- smooths traffic spikes
- provides retry behavior

Trade-off:
- writes are no longer immediate
- slight write latency

Typical use cases:
- logging
- metrics ingestion
- background operations
- notifications

### Load Shedding (Drop Less-Important Writes)

If system is overwhelmed, we may:
- reject some writes
- drop low-priority work
- protect critical systems

Examples:
- drop analytics events but keep payments running
- delay email notifications during outage
- reject spammy write traffic

This is about keeping the core system alive.

### Prioritized Writes

Not all writes are equal.

Example priority levels:

1️⃣ Financial transactions
2️⃣ Inventory updates
3️⃣ User actions
4️⃣ Logs & metrics

In overload conditions:
- high-priority writes continue
- lower-priority ones are queued or dropped

## Why Write Scaling Is Much Harder Than Read Scaling

Reads are “safe” to duplicate.

Writes:
- must be consistent
- must avoid conflicts
- must avoid duplicate execution
- must preserve ordering (sometimes)

That’s why write-scaling tools tend to introduce:
- queuing
- batching
- eventual consistency
- replication complexity
- operational overhead

And why most companies avoid sharding until absolutely necessary.

## Rules of Thumb (Interview-Level Intuition)

Use in this order:

1️⃣ First — optimize schema & queries
2️⃣ Then — add batching & background workers
3️⃣ Then — split services (vertical partitioning)
4️⃣ Only when needed — shard horizontally

Sharding is powerful — but it complicates everything:
- cross-shard joins
- transactions
- migrations
- resharding
- operational complexity

That’s why interviewers want to see:

👉 you scale reads first
👉 you batch writes where possible
👉 you shard only when bottlenecks force you

# Handling Large Blobs

Blobs = Binary Large Objects such as:
- images
- videos
- PDFs
- audio files
- backups / archives

These files are:
- large (MB → GB → TB)
- slow to upload / download
- expensive to process
- bad for relational DB storage

If you try to send them through your application server: 

Client → App Server → DB / Disk

- server becomes a bottleneck
- high memory usage
- request timeouts
- costly bandwidth
- poor scalability

So instead:
👉 We take the app server out of the data path.

## Direct Upload / Download to Blob Storage

Instead of uploading files through your backend, the client uploads directly to storage.

Your backend only issues a temporary, restricted access token.

Flow:
- Client → Backend (ask permission)
- Backend → returns presigned URL
- Client → uploads directly to S3 / GCS / Azure Blob

Your app server never touches the file.
✔ cheaper
✔ faster
✔ easier to scale

## What is a presigned URL?

A temporary URL that allows upload or download of ONE specific file for a limited time.

Example permissions:
- valid for 10 minutes
- only upload allowed
- only for one file key
- max file size allowed

So instead of:
❌ exposing storage credentials
❌ or keeping storage public

We grant controlled, time-limited access.

## Upload workflow (simplified)

1. Client requests an upload
    POST /files/start-upload
2. Backend creates metadata in DB
  - file id
  - user id
  - status = "pending"
3. Backend generates presigned upload URL
4. Client uploads file directly to storage
5. Storage triggers an event (or callback)
6. Backend marks file as:
  - status = uploaded
  - size = xxM
  - hash = ...

## Download workflow

Clients don’t fetch files from your server.

Instead:
- Client → backend (auth check)
- Backend → returns signed download URL
- Client → downloads via CDN

Advantages:
✔ CDN caching
✔ low latency worldwide
✔ reduces backend load

## Why NOT store blobs in databases?

Relational DBs are optimized for:
✔ small rows
✔ structured data
✔ transactions

They are BAD at:
❌ huge objects
❌ streaming IO
❌ high-bandwidth transfer
❌ large backups

Problems if blobs live in DB:
- backup files explode in size
- replication slows
- queries degrade
- restore time becomes hours/days
- storage becomes very expensive

Blob storage solves this:
✔ cheap
✔ highly durable
✔ infinite scale
✔ streaming optimized

## Real-world challenges & how they’re handled

1) 🟡 Upload succeeds but DB not updated

File exists in storage but DB still shows pending.

Fix: use event notifications

Examples:
- S3 event → SNS / SQS / Lambda
- GCS event → Pub/Sub
- Azure event → EventGrid

Then backend reconciles:

if file exists in storage
  update DB → uploaded

2) 🟡 DB says file exists but upload failed

File row exists but user never finished upload.

Solutions:
- expiration timeout
- garbage collection job
- periodic storage scan

3) 🔴 Orphaned files (storage but no DB record)

Causes:
- client uploads with old URL
- upload retried after cancellation

Solution:
- prefix files by tenant/user
- delete unknown keys
- audit with periodic sync

4) 🟡 Security & access control

We must ensure:
- users only upload to allowed keys
- no overriding others’ files
- no infinite upload spam

Best practices:
✔ validate filename + mime type
✔ enforce size limits
✔ generate object names server-side
✔ store ownership in DB
✔ time-limited presigned URLs

Never let clients pick:
/avatars/john.jpg

Instead use:
/users/123/avatars/9f84a3c2.png

## Where lifecycle management comes in

Large files rarely live forever.

Typical lifecycle rules:
- delete old temporary uploads
- archive inactive files
- move large files to cheaper storage
- delete failed uploads after N days

Blob storage supports:
✔ retention policies
✔ storage tiers
✔ lifecycle transitions

## This keeps storage costs under control.

Where this pattern is commonly used
- file upload portals
- profile pictures
- messaging attachments
- video platforms
- document management
- mobile sync apps
- SaaS storage features
- analytics ingestion

It is industry-standard practice today.

## Why this pattern works so well

✔ app server stays small + stateless
✔ uploads don’t crash backend
✔ storage scales infinitely
✔ CDNs make downloads fast
✔ bandwidth cost is reduced
✔ uploads can resume
✔ clients get progress tracking
✔ easier horizontal scaling

## When this pattern may NOT fit

- strict compliance requires server inspection
- inline processing required (virus scan, OCR, etc.)
- extremely low-latency streaming systems

In those cases, files may still pass through:
- a processing pipeline
- malware scanner
- transcoder
- message bus

But even then — storage remains the final system of record.

## Summary

Don’t upload large files through your backend.

Instead:
- backend issues a presigned URL
- client uploads directly to blob storage
- metadata lives in DB
- files delivered through CDN + signed URLs
- storage events keep DB state consistent

Benefits:
✔ backend avoids large payloads
✔ huge scalability & lower cost
✔ resumable uploads & progress
✔ global delivery performance

Challenges to handle:
- DB ↔ storage sync
- failed / abandoned uploads
- orphaned blobs
- lifecycle + retention

This is the standard way modern systems handle large files.

# Multi-Step Processes

Some actions in a system are not a single request.

They involve:
- multiple services
- multiple steps
- external APIs
- long-running operations
- failures & retries

Examples:
- placing an order
- onboarding a new user
- booking a flight
- processing a loan
- uploading & scanning a document
- subscription signup & payment
- refund workflows

A request may take:
- seconds
- minutes
- hours
- even days

👉 It cannot be handled by a single HTTP request / DB transaction.

## What goes wrong without a workflow pattern?

If each service just calls the next one…

Service A → Service B → Service C

then failures become painful:
- one step succeeds
- second step fails
- system ends in half-finished state

Examples:

Payment succeeds 💳
Order not created 📦

Or:

User account created 👤
Email service fails ✉️

Or:

Shipment created 🚚
Inventory not deducted 🏷️

You now need:
- compensation logic
- retries
- manual cleanup
- support tickets
- angry users 😅

This is called:

“Scattered state & ad-hoc error handling”

Multi-step workflow patterns exist to fix this.

## Core idea: Treat workflows as state machines

Each step is tracked as explicit workflow state.

Example order workflow:
- Order Created
- Payment Authorized
- Inventory Reserved
- Shipment Created
- Order Completed

If something fails:
- workflow pauses
- retries automatically
- or rolls back safely

The state is durable — survives crashes & restarts.

🟡 Option 1 — Simple In-App Orchestrator

Backend service stores:
- current step
- inputs
- status

Then runs steps manually:

do step1()
save progress
do step2()
save progress
...

Pros:
✔ simplest
✔ easy to start
✔ works in monoliths

Cons:
❌ hard to retry safely
❌ error handling grows messy
❌ state stored in random tables
❌ debugging is painful

This works early-stage but breaks at scale.

🟣 Option 2 — Event-Driven / Choreography

Each step emits an event, next service reacts.

Example:
- order_created
- payment_authorized
- inventory_reserved
- shipment_created
- order_completed

Nobody coordinates directly — steps are triggered by events.

Pros:
✔ loosely coupled
✔ scalable
✔ natural for microservices

Cons:
❌ debugging is hard
❌ ordering issues
❌ retries can duplicate events
❌ no single “view” of progress

This is good when:
- steps are independent
- ordering isn't strict
- system is already event-driven

🔵 Option 3 — Workflow Engine (Orchestration)

Instead of managing workflow logic yourself…

You describe the workflow and the engine guarantees execution.

Examples:
- Temporal
- AWS Step Functions
- Camunda
- Netflix Conductor
- Apache Airflow (batch-style)

The workflow engine:
✔ stores workflow state
✔ retries safely
✔ resumes after crashes
✔ ensures exactly-once execution
✔ keeps a full audit log

Code example (conceptually):

Step 1: charge payment
Step 2: reserve inventory
Step 3: create shipment


If Step 2 fails:

it retries automatically

or triggers compensation logic

You don’t write retry loops manually —
the workflow system does.

## Why this matters in real systems

Real workflows are messy:
- APIs time out
- workers crash
- messages duplicate
- requests retry
- machines restart
- users cancel mid-flow

A workflow engine guarantees:
- progress is never lost
- a step is not executed twice
- the system knows where it stopped
- failures are recoverable
- every action is auditable

This is critical for:
- fintech
- logistics
- travel systems
- healthcare
- compliance
- enterprise systems

## Where compensation fits in

Not everything can be rolled back with ACID transactions.

Example:

Payment already captured → You must create compensation actions:
- issue refund
- release inventory
- cancel shipment

Workflow engines model this as:

“forward steps” + “undo steps”

This is also known as the Saga pattern.

## Key mindset shift

Instead of: "Call APIs and hope nothing fails"

We move to: "Declare the workflow — let the system guarantee completion."

The system tracks:
- state
- retries
- errors
- timeouts
- compensations
- execution history

This produces:

✔ higher reliability
✔ easier debugging
✔ safer distributed operations

## Summary

Multi-step processes exist when a business action requires:
- multiple services
- long-running operations
- retries + failure handling

Examples:
- order processing
- onboarding
- payments
- shipping
- document verification

Three main approaches:

1️⃣ Manual orchestration in your service

✔ simple
❌ brittle at scale

2️⃣ Event-driven choreography

✔ scalable but decentralized
❌ hard to track & debug

3️⃣ Workflow engines (best for complex flows)

✔ durable state
✔ automatic retries
✔ guarantees exactly-once execution
✔ full audit trail

Modern tools:
- Temporal
- Step Functions
- Camunda
- Conductor

The key idea:

👉 Stop scattering workflow logic everywhere —
declare the steps and let the workflow engine ensure correctness.

# Proximity-Based Services

Whenever a system needs to answer questions like:
- “Find drivers within 2km”
- “Show nearby restaurants”
- “Which stores deliver to this address?”
- “What warehouse is closest to this user?”

…it needs a way to:

👉 efficiently search things by distance / location.

Doing this naively (looping through everything and calculating distance) becomes too slow when there are:
- hundreds of thousands
- or millions of items

So we use geospatial indexes.

## Core idea

Instead of storing: (lat, lon)

as plain numbers…

we store them in a special index that knows how to:
✔ group nearby locations
✔ skip irrelevant regions
✔ search only the nearby area

This makes:
- “find things near me”
- “search radius around point”
- “sort by distance”
fast & scalable.

## Common technologies used

Most proximity systems aren’t built from scratch — instead they use:

✅ Database extensions

Postgres + PostGIS
MySQL spatial indexes

Use when:
- you already use SQL
- results must be transactional + consistent

✅ In-memory caches with geo support

Redis GEO commands (GEOADD / GEORADIUS / GEOSEARCH)

Use when:
- you need very fast lookups
- data changes often (like drivers)

✅ Search engines with geo queries

Elasticsearch / OpenSearch

Use when:
- you need text + geo filtering
- you have huge datasets

## How geo search actually works (mental model)

Instead of thinking about the whole world…

The system:

👉 divides the map into grid cells / tiles

Example:

  [ cell A1 ][ cell A2 ][ cell A3 ]
  [ cell B1 ][ cell B2 ][ cell B3 ]
  [ cell C1 ][ cell C2 ][ cell C3 ]

Then when you ask: “Find drivers within 3km”

The system:
- Finds which cell the user is in
- Searches only that cell + neighbors
- Ignores everything else

This avoids scanning the whole database.

## Important real-world constraint

Most systems don’t search globally.

People don’t say: “find me a driver anywhere in the world”

They say: “find one near me”

So the system can assume:
- queries are local
- search radius is small
- results are regional

This helps massively with performance & design.

🟡 When you don’t need geospatial indexing

If you only have:
✔ a few hundred
✔ or maybe 1–2k items

then…

👉 scanning everything is actually faster + simpler

No special index needed.

Interviewers love to see this trade-off thinking:

“I’d start simple — if volume grows, then introduce geo-indexing.”

That shows maturity.

🟣 When geo-indexes become necessary

They start to matter when you have:
- many drivers / couriers
- many POIs / stores / restaurants
- frequent updates to positions
- high request volume

Typically:

👉 100k+ moving entities
👉 millions of requests

Then brute-force distance search collapses.

## Summary

Proximity-based services: Efficiently finding things near a location.

Instead of scanning everything, we:
✔ split the world into regions
✔ index items inside those regions
✔ search only nearby areas

Common tools:
- PostGIS (PostgreSQL)
- Redis GEO
- Elasticsearch geo-queries

Use geo-indexing when:
- you have hundreds-of-thousands+ items
- or lots of real-time proximity queries

Otherwise:

👉 scanning everything is simpler and totally fine.

# Pattern Selection — Key Notes

Patterns are composable

Real systems often use multiple patterns together.

Example video platform might combine:
- Large Blobs → video uploads
- Long-Running Tasks → transcoding jobs
- Realtime Updates → progress notifications
- Multi-Step Processes / Workflow → coordinate the pipeline

👉 Patterns are not alternatives — they often work as a pipeline.

## The Core Idea — Choose Patterns by Trade-offs

Good architecture is about:
- understanding what problem you’re solving
- selecting only the patterns you actually need
- accepting the trade-offs of each choice

Examples of trade-off themes:
- latency vs throughput
- simplicity vs flexibility
- consistency vs scalability
- cost vs operational complexity

## Start Simple — Scale When Needed

Recommendation path:

1️⃣ Begin with simpler solutions
- polling instead of push
- single-server orchestration
- synchronous processing when possible

2️⃣ Only add complexity when:
- scale demands it
- latency requirements justify it
- reliability or coordination breaks down
- you outgrow a simple design

👉 Avoid prematurely jumping to:
- workflow engines
- event busses
- distributed schedulers
- microservices orchestration

Use them when the problem forces you there.

## Interview Relevance — What Good Candidates Do

Strong candidates:
- proactively recognize applicable patterns
- justify why they choose one over another
- discuss failure modes & trade-offs
- avoid deep implementation details too early

This signals:
- architectural maturity
- ability to reason at system level
- focus on constraints rather than buzzwords
