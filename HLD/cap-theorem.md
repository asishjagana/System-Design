# CAP Theorem

The CAP theorem (also known as Brewer's theorem) states that it is impossible for a distributed data store to simultaneously provide more than two out of the following three guarantees.

## 1. The Three Pillars (C-A-P)

### Consistency (C)
Every read receives the most recent write or an error. In a consistent system, once data is written to one node, it is instantly replicated to all other nodes before a success is returned.
- **Analogy**: If you update your profile picture, everyone sees the new one immediately.

### Availability (A)
Every request receives a (non-error) response, without the guarantee that it contains the most recent write. The system remains operational even if some nodes are down.
- **Analogy**: You can always see *a* profile picture, even if it's the old one while the new one is propagating.

### Partition Tolerance (P)
The system continues to operate despite an arbitrary number of messages being dropped (or delayed) by the network between nodes. In a distributed system, network failures (partitions) are inevitable.
- **Analogy**: The system still works even if the data center in New York can't talk to the one in London.

---

## 2. The Core Choice: Picking Two

In a distributed system, you **must** have Partition Tolerance (P). Networks *will* fail. Therefore, the choice is actually between **Consistency** and **Availability** during a partition.

### CP (Consistency + Partition Tolerance)
If a partition occurs, the system will stop accepting writes or returning reads if it cannot guarantee consistency. It chooses to be "Unavailable" rather than return stale data.
- **Use Case**: Banking/Financial systems, where data accuracy is critical.
- **Examples**: MongoDB (with majority read/write concern), HBase, Zookeeper.

### AP (Availability + Partition Tolerance)
If a partition occurs, nodes will stay available and keep accepting writes/reads, even if they can't sync with other nodes. Data will be "Eventually Consistent".
- **Use Case**: Social Media feeds, Caching, where being online is more important than being 100% up-to-date.
- **Examples**: Cassandra, DynamoDB, CouchDB.

### CA (Consistency + Availability)
Possible only in single-node systems or systems where the network is guaranteed to never fail (which doesn't exist in the real world). Most relational databases (RDBMS) on a single machine are CA.

---

## 3. Real-World Database Mapping

| Database | CAP Category | Note |
| :--- | :--- | :--- |
| **MySQL / Postgres** | **CA** | Traditional RDBMS (ACID). Consistency is sacrificed if you add sharding/async replication. |
| **Cassandra** | **AP** | High availability, eventual consistency. |
| **MongoDB** | **CP** | Generally CP, but highly tunable (can be AP depending on config). |
| **Zookeeper** | **CP** | Used for coordination where consistency is mandatory. |
| **DynamoDB** | **AP** | Tunable, but defaults to high availability. |

---

## 4. Advanced: PACELC Theorem

The CAP theorem only describes what happens when there is a **P**artition. But what happens during normal operation? 

**PACELC** stands for:
- If there is a **P**artition, choose between **A**vailability or **C**onsistency.
- **E**lse (Normal operation), choose between **L**atency or **C**onsistency.

This explains why even the most "Consistent" systems might allow some "Eventual Consistency" to reduce latency (speed up the app).

---

## 5. Summary Table

| Choice | Strategy | Trade-off |
| :--- | :--- | :--- |
| **CP** | Wait for all nodes to sync. | Higher Latency, Potential Unavailability. |
| **AP** | Return data from the local node. | Risk of returning stale/old data. |
| **PACELC** | Balance Speed vs. Accuracy. | Choosing "Fast but potentially old" vs "Slow but accurate". |
