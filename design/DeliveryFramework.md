# Delivery Framework

- [Delivery Framework](#delivery-framework)
- [Requirements (~5 minutes)](#requirements-5-minutes)
  - [Functional requirements](#functional-requirements)
    - [Prioritize top 3 feature](#prioritize-top-3-feature)
  - [Non-functional Requirements](#non-functional-requirements)
  - [Quantify and contextualize requirements](#quantify-and-contextualize-requirements)
- [Core Entities (Notes)](#core-entities-notes)
  - [How to Identify Them](#how-to-identify-them)
  - [Key Principles](#key-principles)
  - [Example (Twitter)](#example-twitter)
  - [Interview Tips](#interview-tips)
- [API / System Interface](#api--system-interface)
  - [Key Steps](#key-steps)
  - [API Protocol Choices](#api-protocol-choices)
  - [Designing REST Endpoints](#designing-rest-endpoints)
  - [Tips for Interviews](#tips-for-interviews)
- [High-Level Design](#high-level-design)
  - [Key Principles](#key-principles-1)
  - [Recommended Components](#recommended-components)
  - [Interview Tips](#interview-tips-1)
- [Deep Dives (Notes)](#deep-dives-notes)
  - [Key Goals](#key-goals)
  - [Example — Twitter Feed](#example--twitter-feed)
  - [Interview Tips](#interview-tips-2)
  - [Common Mistakes](#common-mistakes)

# Requirements (~5 minutes)

The goal of the requirements section is to get a clear understanding of the system that you are being asked to design. 

## Functional requirements 

Functional requirements are your "Users/Clients should be able to..." statements. These are the core features of your system and should be the first thing you discuss with your interviewer. Oftentimes this is a back and forth with your interviewer.

Ask clarifying questions such as:
- “Does the system need to support X?”
- “What should happen when Y occurs?”
- “Is feature A more important than feature B?”

Example (Twitter-like system)

Top functional requirements might be:
- Users should be able to post tweets
- Users should be able to follow other users
- Users should be able to see tweets from users they follow


### Prioritize top 3 feature

Keep your requirements targeted! The main objective in the remaining part of the interview is to develop a system that meets the requirements you've identified -- so it's crucial to be strategic in your prioritization. Many of these systems have hundreds of features, but it's your job to identify and prioritize the top 3. Having a long list of requirements will hurt you more than it will help you and many top FAANGs directly evaluate you on your ability to focus on what matters.


## Non-functional Requirements

Non-functional requirements describe system qualities & behavior under real-world conditions, phrased as:

“The system should be able to …”

“The system should be …”

They define how well the system performs — not what features it has.

✅ Example (Twitter-like system)

Non-functional requirements might include:
- The system should be highly available, prioritizing availability over consistency
- The system should support 100M+ daily active users
- The system should be low-latency, rendering feeds in < 200ms

## Quantify and contextualize requirements

Bad requirement:

“The system should be low latency.”

Too generic — applies to everything.

Better requirement:

“Feed rendering should be < 200ms P95.”

This clarifies:
✔ which part of the system matters most
✔ a measurable engineering target

Where possible:
- specify WHAT subsystem
- specify HOW MUCH (latency, users, throughput, durability)
- specify WHY (business context)

🧰 Checklist to identify the right non-functional requirements

Pick the top 3–5 most relevant to the problem.

🟡 CAP Theorem

Should the system prefer:
- Consistency (banking, inventory, payments)
- Availability (social feeds, messaging)
- Partition tolerance is always assumed.

🌍 Environment Constraints

Ask whether it runs on:
- mobile / battery-limited devices
- low-memory or low-bandwidth networks (3G streaming, IoT)

📈 Scalability

What needs to scale?
- reads vs writes
- burst traffic (sales events, holidays)
- regional usage spikes
- hot users / hot content

⚡ Latency

Where must responses be fast?

Examples:
- feed load < 200ms
- search results < 500ms
- checkout < 1s

Focus especially on paths with computation.

💾 Durability
- How important is preventing data loss?
- Social network — tolerates small loss
- Banking / ledgers — must be durable

🔐 Security

Consider:
- data protection
- auth & access control
- regulatory constraints (GDPR, PCI, HIPAA)

🧯 Fault Tolerance

How resilient must it be?
- redundancy
- failover strategy
- recovery guarantees

📜 Compliance

Legal or industry requirements?

- financial reporting
- privacy laws
- safety / audit obligations

🧠 TL;DR

Non-functional requirements define system qualities, not features.
- Contextualize them (which subsystem matters most?)
- Quantify them (latency, scale, durability targets)
- Prioritize the 3–5 most important constraints

Good candidates identify constraints that actually shape the architecture.

# Core Entities (Notes)

Core entities are the main “things” your system works with.

They form the foundation of your data model

Represent objects your API exchanges and your system persists

## How to Identify Them

Ask yourself:

1️⃣ Who are the actors in the system?
2️⃣ What resources / nouns satisfy the functional requirements?
3️⃣ Which entities are central to the core workflows?

## Key Principles

Start small — don’t list the full data model yet

You’ll discover additional entities as you design

Iterate — add relationships and fields as you go

Name well — good entity names show clarity of thought

## Example (Twitter)

Top core entities:

User — represents a person

Tweet — the content posted

Follow — connection between users

## Interview Tips

Jot down entities quickly (~2 minutes)

Explain that this is a first draft

Show awareness that more entities/relationships will be discovered during design

Using meaningful names is noticed by interviewers — even in small examples

# API / System Interface

Define the contract between your system and its users before designing the internals.

Ensures your high-level design meets functional requirements

Clarifies how clients interact with your system

## Key Steps

1️⃣ Map APIs to core entities from your functional requirements
2️⃣ Decide API protocol
3️⃣ Define resource names, endpoints, and request/response structure

## API Protocol Choices

REST:	Default choice; CRUD-oriented, uses HTTP verbs

GraphQL:	Clients request exactly what they need; useful for multiple client types

RPC / gRPC:	Fast, action-oriented; good for internal service-to-service calls

Tip: Default to REST unless there’s a specific reason to choose otherwise.

For real-time updates, consider WebSockets or Server-Sent Events, but design core REST APIs first.

## Designing REST Endpoints

Use plural nouns for resources: /tweets, /follows

Map CRUD operations to HTTP verbs:

POST	/v1/tweets	create a tweet
GET	    /v1/tweets/{tweetId}	fetch a tweet
POST	/v1/follows	follow a user
GET	    /v1/feed	get user feed (array of tweets)

Derive current user from auth token, not request body

Never trust sensitive info (like user IDs) from clients

## Tips for Interviews

- Link APIs directly to functional requirements
- Keep endpoints clear, consistent, RESTful
- Mention authentication / authorization early
- Discuss real-time endpoints only after the core API

# High-Level Design

High-level design (HLD) is about visualizing your system architecture:
- Identify major components (servers, databases, caches, queues, etc.)
- Show how data flows between components
- Ensure your system meets API / functional requirements

## Key Principles

1️⃣ Start with core requirements
- Begin with your API endpoints
- Build the design sequentially, endpoint by endpoint

2️⃣ Keep it simple first
- Focus on satisfying functional requirements
- Layer on complexity later for non-functional requirements (scalability, latency, availability)

3️⃣ Talk through your thought process
- Explain why each component exists
- Describe data flow and state changes
- Highlight where caches, queues, or async processing could be added

4️⃣ Database & entities
- Document only columns/fields relevant to your design
- Avoid obvious fields (like name or password_hash)
- Add notes next to the database visually for clarity

## Recommended Components

Common HLD building blocks:
- Web/API servers → handle client requests
- Databases → store core entities
- Caches → speed up reads / reduce DB load
- Message queues / Pub-Sub → decouple services, handle async tasks
- Blob storage / CDN → for large files
- Load balancers → distribute traffic
- Workers / background processors → handle long-running jobs

## Interview Tips
- Use boxes & arrows to represent components & data flow
- Highlight async areas (queues, workers) without overcomplicating
- Show where state changes (DB, cache, queues)
- Be verbal — explain assumptions and reasoning
- Avoid over-engineering → interviewer cares more about clarity & completeness

# Deep Dives (Notes)

Deep dives are about hardening your high-level design to meet non-functional requirements and handle real-world challenges.

Usually done in the last 10 minutes of the interview

Focuses on improving scalability, performance, and robustness

## Key Goals

1️⃣ Meet non-functional requirements

e.g., low latency, high availability, throughput, durability

2️⃣ Address edge cases

unusual scenarios, failure modes, spikes in traffic

3️⃣ Identify bottlenecks

database hotspots, slow queries, network congestion

4️⃣ Iteratively improve design

introduce caches, queues, sharding, async processing

## Example — Twitter Feed

Functional requirement: fetch feeds

Non-functional requirements:
- 100M DAU
- low-latency feed retrieval

Deep dive improvements:
- Horizontal scaling → multiple web & API servers
- Caching → Redis / in-memory caches for user timelines
- Database sharding → distribute user/feed data across multiple DBs

Fanout strategies:
- Fanout-on-write → precompute feeds for users when tweets are posted
- Fanout-on-read → compute feeds on demand at read time
- Queueing / async processing → offload heavy work from request path

## Interview Tips

Be proactive: Identify areas for improvement yourself (more expected for senior candidates)

Balance talking: Let interviewer probe; don’t dominate the conversation

Explain trade-offs:
- Latency vs throughput
- Consistency vs availability
- Pre-computation vs on-demand computation
- Iteratively update design: Draw new components, arrows, or annotations

## Common Mistakes
- Talking too much without checking interviewer signals
- Ignoring non-functional requirements
- Forgetting to justify design changes with trade-offs


