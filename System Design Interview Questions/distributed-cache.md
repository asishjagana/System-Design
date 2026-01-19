# Design a Distributed Cache (Redis Internals)

How to design a high-throughput, low-latency key-value store.

## 1. Requirements
- **Performance**: Sub-millisecond latency.
- **Scalability**: Handle terabytes of data across multiple nodes.
- **Availability**: System stays up if a node fails.
- **Persistence**: (Optional) Save data to disk.

## 2. Data Structures in Memory
- **Hash Table**: $O(1)$ average time complexity for GET/SET.
- **LRU (Least Recently Used) Chain**: For automatic memory management.

## 3. Distributed Strategy: Consistent Hashing
How to decide which node stores key `user:123`?
Traditional `hash(key) % N` fails when $N$ (number of servers) changes.
- **Consistent Hashing**: Minimizes data movement when servers are added/removed. Only $K/N$ keys need to be remapped.

## 4. Replication & High Availability
- **Master-Slave**: Writes to master, reads from slaves.
- **Sentinel/Coordinator**: Monitors nodes and triggers "Failover" if the master dies (elects a new slave as master).

## 5. Eviction Policies
What happens when RAM is full?
1. **No Eviction**: Return error on writes.
2. **AllKeys LRU**: Evict least recently used key.
3. **Volatile LRU**: Only evict keys with an `Expiry` (TTL) set.

## 6. Advanced: The Single-Threaded Debate
Redis is famously single-threaded for its core operations.
- **Why?** It avoids the overhead of context switching and locks. Memory/Network is the bottleneck, not the CPU.
- **Modern**: Latest Redis uses multi-threading for I/O (parsing network packets) but keeps the data execution single-threaded.

## 7. Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Blazing Fast**: Millions of ops/sec on a single commodity server. | **RAM Expense**: Holding all data in memory is costly. |
| **Reduces DB Load**: Acts as a shield for your primary database. | **Data Consistency**: Cache and DB can get out of sync. |

## 8. Real-Time Use Case
Used as a **Session Store**, **Real-time Leaderboards** (using Sorted Sets), or **Pub/Sub** messaging.
