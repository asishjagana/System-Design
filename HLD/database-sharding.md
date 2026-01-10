# Database Sharding

Sharding is a method for distributing data across multiple machines. It is the architectural technique of horizontal partitioning.

## Real-Time Use Case
**Instagram User Data**: When Instagram hit millions of users, a single Postgres database could not handle all user profiles, posts, and follows.
- **Solution**: They sharded the data by `UserId`. 
  - User IDs 1-1,000,000 go to DB Server 1.
  - User IDs 1,000,001 - 2,000,000 go to DB Server 2.

## Sharding Strategies
1. **Key-Based (Algorithmic)**: Use a hash of a key (e.g., `UserId % NumberOfShards`) to find the server.
2. **Range-Based**: Shard based on a range (e.g., Alphabetical A-M, N-Z).
3. **Directory-Based**: Use a lookup table (service) to find which shard contains the data.

## Key Challenges
- **Resharding**: If you need to add more shards, moving data is hard.
- **Join Operations**: Performing joins across shards is extremely difficult and slow.
- **Hotspots**: One shard might get significantly more traffic than others.

## Example Concept

```csharp
// Simple Shard Resolver
public string GetShardConnection(int userId) {
    int shardCount = 4;
    int shardId = userId % shardCount;
    return $"ConnectionString_Shard_{shardId}";
}
```
