# Uber System Design

Uber is a ride-sharing service that matches riders with drivers based on proximity and other factors. Designing Uber requires solving complex geospatial problems at massive scale.

---

## 1. Requirements

### Functional Requirements
- **Update Location**: Drivers should be able to update their GPS coordinates every few seconds.
- **Request Ride**: Riders can request a ride by providing pickup and destination locations.
- **Matching**: The system should find the "best" available driver nearby.
- **Ride Tracking**: Riders can track the driver's location in real-time.

### Non-Functional Requirements
- **High Availability**: The matching and tracking system must always be online.
- **Low Latency**: Matching should happen in under a minute; GPS updates should feel real-time.
- **Scalability**: Must handle millions of location updates and thousands of ride requests concurrently.

---

## 2. The Core Problem: Geospatial Indexing

The biggest challenge is: "Find all available drivers within a 3-mile radius of this rider."
Traditional SQL `SELECT * FROM drivers WHERE lat/long...` is slow because it requires a full table scan as drivers move.

### Solutions:
- **GeoHash**: Dividing the world into a grid of cells. Each cell has a unique string ID. Nearby locations share prefix characters.
- **QuadTrees**: A tree data structure where each node has four children. If a node (region) has too many drivers, it splits into four smaller squares.
- **Google S2**: A library that maps a sphere onto a 1D Hilbert curve. Extremely efficient for mathematical operations on map regions.

---

## 3. High-Level Architecture

### A. Location Service (Write-Heavy)
Handles GPS updates from drivers. Drivers send their `(longitude, latitude)` every 3-5 seconds.
- **Choice**: Uses **Redis** or a specialized Geospatial DB to store current locations for sub-millisecond access.

### B. Dispatch Service (Matchmaker)
The brain of the system. 
1. Receives a ride request.
2. Queries the Location Service for nearby drivers (using QuadTree/S2).
3. Filters drivers by status (e.g., active, not currently in a trip).
4. Notifies drivers via **WebSockets**.

### C. Trip Service
Manages the lifecycle of a trip: "Driver Accepted" -> "Pickup" -> "In Progress" -> "Completed."

---

## 4. Component Deep Dives

### Matching Algorithm
Matching isn't just about the closest driver:
- **ETAs**: Uses historical and real-time traffic data.
- **Batch Matching**: Instead of matching the very first driver found, Uber might wait 5-10 seconds to batch multiple riders and drivers to optimize efficiency across the whole city.

### Handling Concurrency
If two riders are near the same driver, we must ensure only one gets the ride.
- **Solution**: Use **Distributed Locking** (e.g., Redis Redlock) or **Optimistic Locking** in the DB during the "Accept Ride" transaction.

---

## 5. Scalability and Reliability
- **Kafka**: Used as a central bus for all events (location updates, ride requests, payment triggers).
- **Sharding**: Location data is sharded by region (City/Country) since drivers in London don't need to be matched with riders in New York.
- **Consistent Hashing**: Ensures requests for the same region always hit the same set of servers.

---

## 6. Tech Stack (Real-World)
- **Languages**: Go (high concurrency), Java, Python.
- **Databases**: Cassandra (for high write throughput of history), PostgreSQL (for billing/user data).
- **Communication**: gRPC for internal service calls, WebSockets for rider/driver apps.
- **Infrastructure**: Apache Kafka, Redis, Google S2 Library.

---

## 7. Reference Links
- [Uber Engineering Blog](https://eng.uber.com/)
- [Grokking the System Design: Uber](https://www.educative.io/courses/grokking-the-system-design-interview/3j6Ervj09E1)
- [How Uber Uses S2 for Geospatial Indexing](https://eng.uber.com/go-geofence/)
