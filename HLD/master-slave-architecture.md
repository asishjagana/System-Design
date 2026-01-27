# Master-Slave Architecture

Master-Slave Architecture (also known as Primary-Replica) is a model of communication or control where one device or process (the **Master**) has unidirectional control over one or more other devices or processes (the **Slaves**).

In a database context, the Master is the main node that handles all **Write** operations, while one or more Slaves handle **Read** operations.

---

## 1. How it Works (Replication)

1. **Write Operation**: All data modifications (INSERT, UPDATE, DELETE) are sent to the **Master** node.
2. **Binary Logging**: The Master records these changes in a log (e.g., MySQL BinLog).
3. **Replication**: The **Slaves** connect to the master, read the log, and apply the same changes to their own data sets.
4. **Read Operation**: Clients can read from any of the **Slaves** or the **Master**. Usually, traffic is balanced across slaves to offload the master.

---

## 2. Problems it Solves

### A. Read Scalability
If your app has 90% reads and 10% writes (like Twitter or Instagram), a single server will eventually crash under read pressure. You can add 10 slaves to handle millions of reads while the master stays focused on writes.

### B. High Availability / Redundancy
If the master node catches fire, you haven't lost your data. One of the slaves contains a (mostly) up-to-date copy and can be "promoted" to be the new master.

### C. Analytics and Backups
You can run heavy, slow SQL queries for "Monthly Expense Reports" on a Slave node. Since it's a separate machine, your live customers won't experience any slowness on the website.

---

## 3. Drawbacks and Challenges

### A. Replication Lag
Replication takes time (miliseconds to seconds). If a user updates their profile and immediately refreshes, they might see the old data if their read request hits a slave that hasn't synced yet. This is called **Eventual Consistency**.

### B. Write Bottleneck
You can scale reads infinitely by adding slaves, but you still have only **one Master** for writes. If you have too many writes for one server to handle, you need **Sharding** or **Multi-Master** setups.

### C. Failover Complexity
When the Master dies, the system must detect it, pick the "best" slave, and update all client configurations to point to the new Master. This often requires complex "Orchestration" tools.

### D. Split-Brain Problem
In a network failure, two nodes might both think they are the "Master". If both start accepting writes, your data will become corrupted and irreconcilable.

---

## 4. Real-World Use Cases

- **MySQL/PostgreSQL**: The classic use case for scaling relational databases.
- **Redis**: One master for fast writes, multiple replicas for distributed reads.
- **Elasticsearch**: Uses primary and replica shards for both scale and search speed.

---

## 5. Summary Table

| Feature | Master | Slave |
| :--- | :--- | :--- |
| **Writes** | ✅ Allowed | ❌ Denied (Read-Only) |
| **Reads** | ✅ Allowed | ✅ Encouraged |
| **Scaling** | Vertical (Bigger CPU/RAM) | Horizontal (Add more nodes) |
| **Role** | Source of truth | Backup & Read Provider |
