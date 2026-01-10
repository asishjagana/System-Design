# Database Replication & Consistency

In distributed systems, keeping data identical across multiple servers (Replication) is a core challenge.

## Real-Time Use Case: Global Applications (e.g., Google Spanner)
A user in New York updates their profile. A user in Tokyo should see that update almost immediately. Replication ensures that if the NY data center goes offline, Tokyo (or London) can still serve the profile data.

## Advanced Concepts
### 1. CAP Theorem
You can only have two of the following three in a distributed system:
- **Consistency**: Every read receives the most recent write.
- **Availability**: Every request receives a response (not guaranteed to be the latest).
- **Partition Tolerance**: The system continues to operate despite network failures between nodes.
*In reality, you always need Partition Tolerance, so the choice is between CP and AP.*

### 2. PACELC Theorem (An extension of CAP)
If there is a partition (P), how does the system trade off availability (A) and consistency (C); else (E), when the system is running normally, how does it trade off latency (L) and consistency (C)?

### 3. Replication Topologies
- **Leader-Follower (Master-Slave)**: All writes go to the Leader. Followers handle reads.
- **Multi-Leader**: Multiple nodes can accept writes. Useful for multi-region setups.
- **Leaderless (Quorum-based)**: Any node can accept writes. Used in DynamoDB/Cassandra.

## Pros and Cons
| Strategy | Pros | Cons |
| :--- | :--- | :--- |
| **Synchronous Replication** | Strong consistency; no data loss on failover. | High latency (needs ack from all nodes); fragile. |
| **Asynchronous Replication** | Low latency; Leader isn't blocked by slow followers. | Risk of data loss if Leader crashes before replication. |
| **Quorum Writes (R+W > N)** | High availability and tunable consistency. | Complex to implement; read/write performance trade-offs. |

## Example: Read-Your-Writes Consistency Logic
```csharp
public async Task UpdateAndRead(string userId, string data) {
    // Write to Leader
    await _leaderDb.Update(userId, data);
    
    // To ensure "Read-Your-Writes", we might force the next read from the Leader 
    // or wait for a specific replication timestamp.
    var result = await _leaderDb.Read(userId); 
}
```
