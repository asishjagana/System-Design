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

## Key Benefits
- Reduces Latency.
- Decreases Database Load.
- Improves System Throughput.
