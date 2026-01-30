# CAP Theorem

In a distributed system, you can only guarantee 2 of 3 properties:
- Consistency (C): All nodes return the same, most up-to-date data.
- Availability (A): Every request gets a response (even if data is stale).
- Partition Tolerance (P): The system keeps working despite network failures.

Key insight:
Partition tolerance is non-negotiable in real distributed systems (networks will fail).

So in practice, CAP becomes:
During a network partition, do you choose Consistency or Availability?

CP systems:
- Prefer correctness over uptime
- May reject or block requests to avoid stale data

AP systems:
- Prefer uptime over correctness
- Always respond, but data may be temporarily inconsistent


## Example - Two replicated servers

Two servers normally stay in sync, so everyone sees the latest profile name.
If the connection between them breaks, the system has to choose:
- either block users to avoid showing wrong data (consistency)
- or keep working and show slightly outdated data (availability)
Most real systems choose to keep working, because seeing an old name is better than seeing an error.

## When to choose Consistency (CP systems)

Choose consistency over availability when wrong data is worse than no data:
- Ticket booking: Prevent double-booking the same seat.
- Inventory systems: Avoid overselling limited stock.
- Financial systems: Ensure correct prices and order books.
These systems may reject or block requests during partitions to guarantee correctness.

## When to choose Availability (AP systems)

Choose availability over consistency when temporary wrong data is acceptable:
- Social media: Profile updates can propagate later.
- Content platforms (e.g., Netflix): Old metadata briefly is fine.
- Review sites (e.g., Yelp): Slightly outdated info is better than downtime.

These systems use eventual consistency and always respond, even during partitions.

Rule of thumb:
- Wrong data = catastrophic → Consistency
- Wrong data = tolerable → Availability

## CAP in system design interviews
- CAP should be discussed early in system design interviews.
- After functional requirements, define non-functional requirements.
- Start non-functionals by deciding: Consistency or Availability?
- That choice directly shapes your architecture and trade-offs.

Always ask: “During a partition, do we favor correctness or uptime?”

## Designing for Consistency (CP systems)
- Use distributed transactions (e.g., 2-phase commit) to keep data in sync → higher latency.
- Single-node solutions can simplify consistency but limit scalability
- Tech examples: PostgreSQL, MySQL, Google Spanner, DynamoDB (strong consistency mode).

## Designing for Availability (AP systems)
- Multiple replicas with asynchronous replication → always available reads, may be slightly stale.
- Change Data Capture (CDC) → updates propagate eventually, keeping the primary system responsive.
- Tech examples: Cassandra, DynamoDB (multi-AZ), Redis clusters.

## Advanced CAP Theorem Considerations
- Real-world systems often mix CAP choices by feature
- Not always binary: Different parts of a system can prioritize different trade-offs.

Example: Ticketmaster
- Booking a seat → Consistency (no double-booking)
- Viewing event details → Availability (slightly stale info is fine)

Example: Tinder
- Matching → Consistency (both users see match immediately)
- Viewing profiles → Availability (stale profile pics okay)

- Choose consistency for critical operations
- Availability for read-heavy or non-critical features.

## Levels of Consistency in CAP
- Strong Consistency: All reads show the latest write 
  - → expensive, needed for critical systems (e.g., banking).
- Causal Consistency: Related actions appear in order 
  - → ensures logical flow (e.g., comments after posts).
- Read-your-own-writes: Users see their own updates immediately, others may see stale data 
  - → common in social apps.
- Eventual Consistency: System becomes consistent over time 
  - → temporary inconsistencies okay, used in high-availability systems (e.g., DNS, distributed DBs).

Key:
- Stronger consistency → slower
- Eventual consistency → faster & more available.
