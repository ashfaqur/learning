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

Client asks the server: “Hold this request open until something happens.”

Server replies only when there’s an update.

Then client immediately opens another request.

This reduces waste.

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

This is like taking a key to a bathroom stall — others must wait.

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

Idea:
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

That shows maturity.
