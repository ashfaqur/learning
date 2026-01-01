# Database Modelling

- [Database Modelling](#database-modelling)
  - [Database Model Options](#database-model-options)
  - [How Database Indexing Works?](#how-database-indexing-works)
  - [Normalization vs Denormalization](#normalization-vs-denormalization)
  - [Scaling and Sharding](#scaling-and-sharding)
  - [ACID Transactions](#acid-transactions)
    - [Atomicity — “All or Nothing”](#atomicity--all-or-nothing)
    - [Consistency — “Rules are obeyed”](#consistency--rules-are-obeyed)
    - [Isolation — “Transactions don’t interfere”](#isolation--transactions-dont-interfere)
    - [Durability — “Once committed, it stays”](#durability--once-committed-it-stays)
    - [Why ACID Matters for Contention](#why-acid-matters-for-contention)
    - [Quick Example: Concert Ticket](#quick-example-concert-ticket)


## Database Model Options

Relational (SQL)

Tables with fixed schema, foreign keys, ACID transactions.

Great for well-defined entities (users, posts, orders).

Pros: strong consistency, complex queries, joins.

Cons: joins can be slow at scale; need caching or denormalization.

Examples: PostgreSQL, MySQL, SQLite.

Document (NoSQL)

JSON-like documents with flexible schemas; embeds related data.

Pros: avoids joins, flexible for evolving structures.

Cons: updating nested data can be costly; less enforced consistency.

When to use: rapidly changing schemas, deeply nested data.

Examples: MongoDB, Firestore, CouchDB.

Key-Value Stores

Simple key → value lookups, extremely fast.

Pros: high performance, great for caching, sessions.

Cons: limited query capabilities.

Examples: Redis, DynamoDB.

## How Database Indexing Works?

Indexes are data structures that help the database find records quickly without scanning every row. Think of them like the index in a book - instead of reading every page to find "normalization," you look it up in the index and jump directly to page 149. While data modeling in an interview, you'll typically want to callout which columns are indexed and why.

## Normalization vs Denormalization

Normalization means storing each piece of information in exactly one place. User data lives only in the users table, not duplicated across other tables. This prevents data anomalies where updates happen in one place but not another, leaving your system with inconsistent state.

Denormalization adds redundant data for fast reads; it rarely removes the original source tables. The original table usually stays for correctness and auditing.

## Scaling and Sharding

When your data gets too large for a single database, you need to shard it across multiple machines. The key is choosing a partition strategy that keeps related data together.


## ACID Transactions

ACID is an acronym describing the guarantees a database transaction provides:

A – Atomicity

C – Consistency

I – Isolation

D – Durability

Together, they ensure that multiple operations on the database behave reliably, even in concurrent or failure-prone environments.

### Atomicity — “All or Nothing”

Either all operations in a transaction succeed, or none do.

No partial updates.

Example: Bank transfer

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

If the second UPDATE fails, the first one rolls back

You never end up with money “lost in between”

💡 Think: atomicity = “the transaction is one indivisible unit”

### Consistency — “Rules are obeyed”

Database moves from one valid state to another valid state.

Ensures integrity constraints are maintained:
- foreign keys
- unique keys
- custom business rules

Example: Inventory
- You cannot reduce stock below zero

If transaction violates this → DB rejects it

💡 Think: consistency = “database rules are never broken”

### Isolation — “Transactions don’t interfere”

Concurrent transactions don’t see each other’s intermediate states.

Guarantees that parallel execution is safe.

Isolation Levels:
- Read Uncommitted – can see uncommitted changes (rarely used)
- Read Committed – sees only committed data (default in many DBs)
- Repeatable Read – repeated reads in same transaction see same data
- Serializable – strongest, transactions appear as if run one at a time

Example: Ticket booking
- Two users try to book the same seat

Isolation ensures only one succeeds even if they submit at the same time

💡 Think: isolation = “no dirty reads, no interference”

### Durability — “Once committed, it stays”

Once a transaction commits, its changes survive system crashes

Data is stored safely on disk or replicated storage

Example: Payment transaction
- Money deducted from one account and added to another
- Even if server crashes immediately after commit, transaction is not lost

💡 Think: durability = “committed changes are permanent”

### Why ACID Matters for Contention

Atomicity + Isolation → prevents race conditions

Consistency → prevents invalid states

Durability → prevents data loss under failure

So when multiple users access the same resource concurrently:

ACID transactions in your database ensure correctness automatically

No need for manual locking in many cases

### Quick Example: Concert Ticket

Suppose seat 42 is the last available.

```sql
BEGIN;
SELECT available FROM seats WHERE id = 42;
UPDATE seats SET available = false WHERE id = 42;
COMMIT;
```

Two users try at once
- Isolation ensures only one succeeds
- Atomicity ensures partial updates don’t leave seat in weird state
- Durability ensures once reserved, it’s permanent
- Consistency ensures seat cannot be double-booked

✅ Job done safely by database ACID guarantees.
