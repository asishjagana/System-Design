# Design a Proximity Service (Yelp / Google Maps)

How to find nearby places (restaurants, gas stations) based on a user's location.

## 1. Requirements
- **Search**: Given a (lat, long) and a radius, return all places within that radius.
- **Low Latency**: Results should be returned in < 200ms.
- **Scale**: Support millions of places and high query volume.

## 2. The Core Problem: Geospatial Querying
A traditional SQL query like `SELECT * FROM places WHERE distance(user_lat, user_long, place_lat, place_long) < 10` is $O(N)$ and extremely slow for millions of rows.

## 3. Geospatial Indexing Solutions
### A. Geohash
Divides the world into a grid. Each grid cell is represented by a base32 string.
- If two strings share a long prefix, they are geographically close.
- **Pros**: Easy to store in a standard DB index.
- **Cons**: Border cases (two points very close but in different major grid cells).

### B. QuadTree
A tree data structure where each node has exactly four children. A node is split into four if it contains too many places.
- **Pros**: Naturally handles different densities (more nodes in NYC, fewer in a desert).
- **Cons**: Harder to update in real-time if places move frequently (e.g., Uber drivers).

### C. Google S2 Library
Uses Hilbert Curves to map the 2D sphere to a 1D line while preserving locality. Extremely optimized.

## 4. High-Level Architecture
1. **Load Balancer** routes request.
2. **Search Service** calculates the Geohash for the user's location.
3. Queries the **Geospatial DB** (PostGIS, Redis GEO, or DynamoDB with Geohash).
4. **Place Service** fetches detailed metadata (name, rating, photos) once IDs are found.

## 5. Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Instant Search**: User feels the app is "Live". | **Cold Starts**: Building a QuadTree in memory can take time. |
| **Efficient**: Only processes tiny subsets of the world map. | **Complex Updates**: Moving objects (like food delivery) require high-frequency index updates. |

## 6. Real-Time Use Case
**Uber** uses this to find the nearest drivers. **Tinder** uses it for distance-based matching. **Yelp** uses it to find restaurants.

## 7. Geohash Concept
- `u4pru`: San Francisco
- `u4pru...`: Specific block in SF
By searching for prefix `u4pru`, we find all neighboring points.
