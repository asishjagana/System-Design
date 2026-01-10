# Distributed ID Generator (Snowflake)

How do you generate unique IDs across a distributed system with high throughput and low latency?

## 1. The Problem
In a distributed database (like sharded MySQL), how do you ensure that two different servers don't generate the same ID? 
- `Auto-increment` in a single DB doesn't work for distributed.
- `UUIDs` are 128-bit (too large for indexing) and not naturally ordered by time.

## 2. Twitter's Snowflake Algorithm
The ID is a 64-bit integer composed of:
- **Sign Bit (1 bit)**: Always 0.
- **Timestamp (41 bits)**: Milliseconds since a custom "epoch". Lasts ~69 years.
- **Machine ID (10 bits)**: Supports 1024 unique generator nodes.
- **Sequence Number (12 bits)**: A local counter that increments for每一个 ID generated within the same millisecond on the same machine. (Resets to 0 every millisecond). Supports 4096 IDs/ms per machine.

## 3. Real-Time Use Case: Twitter/X
When you tweet, your Tweet ID needs to be unique across thousands of servers. By using Snowflake, Twitter ensures that IDs are roughly time-ordered (k-sortable), which makes database indexing much faster.

## 4. Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **High Performance**: Generating IDs locally without network calls to a central DB. | **Time Dependent**: If a machine's clock drifts backward, it might generate duplicate IDs. |
| **Scalable**: Simply add more nodes with unique Machine IDs. | **Coordination**: Requires a way to assign unique Machine IDs to nodes (e.g., Zookeeper). |
| **Sortable**: Newer IDs are generally larger than older ones. | |

## 5. Alternatives
- **Database Ticketing Server (Flickr Approach)**: A single DB dedicated to generating IDs. *Cons: Single point of failure.*
- **UUID v4**: Completely random. *Cons: Large, random distribution (bad for DB performance).*
- **UUID v7**: Newer UUID version that is time-ordered.

## 6. Calculation Example
10 bits for Machine ID = $2^{10} = 1024$ machines.
12 bits for Sequence = $2^{12} = 4096$ IDs per ms.
Total Capacity = $1024 \times 4096 = 4.1$ Million IDs per millisecond!
