# Database Modelling

- [Database Modelling](#database-modelling)
  - [Database Model Options](#database-model-options)
  - [How Database Indexing Works?](#how-database-indexing-works)
  - [Normalization vs Denormalization](#normalization-vs-denormalization)
  - [Scaling and Sharding](#scaling-and-sharding)


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
