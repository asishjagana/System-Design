# Design a News Feed System (Facebook / Twitter)

How to design a system that displays a constantly updating list of stories/posts from your friends.

## 1. Requirements
- **Post Creation**: Users can create posts (Text/Media).
- **Feed Generation**: Users see posts from friends/follows in chronological or ranked order.
- **Latency**: Feed must load in < 500ms.
- **Consistency**: New posts should appear in friends' feeds quickly.

## 2. Key Strategies: Push vs. Pull (Fan-out)
### Pull Model (Fan-out on Load)
When a user loads their feed, the system queries all their friends, fetches their latest posts, and merges them.
- **Pros**: Simple write path.
- **Cons**: Reading is extremely slow for users with many friends.

### Push Model (Fan-out on Write)
When a person posts, the system immediately "pushes" that post into the pre-computed "Feed Cache" of all their followers.
- **Pros**: Reading the feed is instant (just fetch from Redis).
- **Cons**: Writing is slow/impossible for celebrities with 100M followers (The "Celebrity Problem").

### Proposed Hybrid Model
Push for regular users. Pull for celebrities (don't push to 100M, just fetch their posts when the follower loads their feed).

## 3. High-Level Architecture
1. **Feed Service**: Aggregates data from the post service and user service.
2. **Post Service**: Stores posts in a distributed DB (Cassandra).
3. **Feed Cache**: Stores the IDs of the last 500 posts for each user in Redis.
4. **Ranking Service**: Uses Machine Learning to sort the feed based on engagement, relevance, and recency.

## 4. Real-Time Use Case: Instagram / Twitter
Twitter uses "Timelines" which are heavily pre-computed. They use a service called "FlockDB" for graph relationships and "Gizzard" for sharding.

## 5. Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **High Scale**: Successfully handles billions of active users. | **Eventually Consistent**: Friends might see the same post at slightly different times. |
| **Optimized Reads**: Using caches makes the app feel extremely responsive. | **Storage Heavy**: Storing pre-computed feeds for every user consumes massive memory. |

## 6. Real-time Implementation Snippet (Fan-out Workflow)
```csharp
public async Task PublishPost(Post post) {
    await _postRepo.Save(post);
    
    // Get followers (Simplified)
    var followers = await _followRepo.GetFollowerIds(post.AuthorId);
    
    foreach (var fid in followers) {
        // Asynchronously add to their feed cache (RabbitMQ/Kafka)
        await _queue.PushToFanout(fid, post.Id);
    }
}
```
