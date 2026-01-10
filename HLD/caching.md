# Caching

Caching is the process of storing copies of files or data in a temporary storage location (cache) so they can be accessed more quickly.

## Real-Time Use Case
**Facebook News Feed**: Fetching a user's news feed from a database every time they refresh is extremely slow and expensive.
- **Cache Strategy**: The application first checks a Distributed Cache (like **Redis**). If the feed data is there (Cache Hit), it's returned instantly. If not (Cache Miss), it fetches from the DB, stores it in Redis for next time, and returns it.

## Caching Strategies
1. **Cache-Aside**: Application checks cache first, then DB.
2. **Read-Through**: Cache sits in-line with the DB and updates itself.
3. **Write-Through**: Data is written to both cache and DB simultaneously.
4. **Write-Back**: Data is written to cache first and DB later.

## Example: C# Redis Implementation

```csharp
using StackExchange.Redis;

public class CacheService
{
    private readonly IDatabase _cache;

    public CacheService()
    {
        var redis = ConnectionMultiplexer.Connect("localhost");
        _cache = redis.GetDatabase();
    }

    public async Task<string> GetUserDataAsync(string userId)
    {
        // Try to get from cache
        string cachedData = await _cache.StringGetAsync(userId);
        
        if (!string.IsNullOrEmpty(cachedData))
        {
            return cachedData; // Cache Hit
        }

        // Cache Miss: Fetch from DB (Simulated)
        string dbData = await FetchFromDatabase(userId);

        // Store in cache for 10 minutes
        await _cache.StringSetAsync(userId, dbData, TimeSpan.FromMinutes(10));

        return dbData;
    }

    private Task<string> FetchFromDatabase(string userId) 
        => Task.FromResult($"User Data for {userId}");
}
```

## Advanced Concepts
### 1. Cache Eviction Policies
When the cache is full, how do we decide what to delete?
- **LRU (Least Recently Used)**: Discard the least recently accessed items first.
- **LFU (Least Frequently Used)**: Discard items that are used the least often.
- **FIFO (First In First Out)**: Traditional queue-based eviction.

### 2. Thundering Herd Problem
When a hot cache key expires, many concurrent requests hit the DB at once.
- **Solution**: Use **Mutual Exclusion (Locking)** so only one request updates the cache while others wait.

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Latency Reduction**: Drastically improves response times for "hot" data. | **Data Stale-ness**: High risk of serving old data if not managed correctly. |
| **Increased Throughput**: One single cache node can serve thousands of requests per second that would crush a DB. | **Cost**: RAM (used for caching) is much more expensive than disk storage. |
| **Cost Savings**: Reduces the expensive "Read Units" billed by Cloud Databases. | **Complexity**: Managing cache invalidation is famously one of the hardest problems in CS. |

## Key Benefits
- Reduces Latency.
- Decreases Database Load.
- Improves System Throughput.
