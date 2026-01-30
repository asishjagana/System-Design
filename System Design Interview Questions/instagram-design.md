# Instagram System Design

Instagram is a photo-sharing service where users can upload photos/videos, follow other users, and view a feed of photos shared by the people they follow.

## 1. Requirements

### Functional Requirements
- **Upload**: Users can upload photos and videos.
- **Search**: Users can search for photos based on video/photo titles or users.
- **Follow**: Users can follow other users.
- **News Feed**: Users can see a feed of photos from all users they follow.

### Non-Functional Requirements
- **High Availability**: The system should be highly available.
- **Reliability**: Any uploaded photo or video should not be lost.
- **Scalability**: The system should be able to scale as user base grows.
- **Low Latency**: Generating news feeds should be fast (latency < 200ms).
- **Eventual Consistency**: It's okay if a user sees a photo slightly later than it was uploaded.

---

## 2. Capacity Estimation
- **Total Users**: 500 million.
- **Daily Active Users (DAU)**: 1 million.
- **New Photos**: 2 million every day (approx. 23 photos per second).
- **Average Photo Size**: 200 KB.
- **Storage for 1 Day**: 2M * 200KB = 400 GB.
- **Storage for 5 Years**: 400GB * 365 * 5 ≈ 730 TB.

---

## 3. High-Level Design

The architecture follows a standard client-server model with various specialized services:

- **Client**: Mobile/Web apps.
- **Load Balancer**: Distributes traffic across web servers.
- **Web/Application Servers**: Handle uploads, searches, and feed requests.
- **Metadata Database**: Stores information about users, photos, and follows.
- **Object Storage**: High-durability storage for the actual photo/video files (e.g., S3).
- **Cache**: In-memory storage for frequently accessed data (feeds, hot photos).

---

## 4. Component Deep Dives

### A. Photo Storage (The "Blob" Store)
Storing billions of photos in a relational database is inefficient. Instead, use a **Distributed Object Store** (like Amazon S3 or Azure Blob Storage).
- **CDN (Content Delivery Network)**: Photos are cached at edge locations globally to reduce latency for users.

### B. Metadata Management
For metadata (User IDs, Photo URLs, Timestamp, Location), we need a database that supports complex queries and is highly scalable.
- **Choice**: **PostgreSQL** (Relational) with sharding or **NoSQL** (Cassandra/DynamoDB) for high write throughput.
- **Schema**:
    - `User`: UserID, Name, Email, CreationDate.
    - `Photo`: PhotoID, UserID, PhotoPath, CreationDate, Location.
    - `Follow`: UserID1, UserID2 (representing 1 follows 2).

### C. News Feed Generation
This is the most time-critical part of the system.
- **Pull Model (Fan-out on Load)**: Clients pull feed data from servers. Hard for frequent users (heavy load on DB).
- **Push Model (Fan-out on Write)**: When a user uploads a photo, it's pushed to their followers' "pre-generated" feeds stored in a cache (e.g., Redis).
- **Hybrid Approach**: Use **Push** for regular users and **Pull** for "Celebrities" (users with millions of followers) to avoid "Thundering Herd" problems.

---

## 5. Scaling and Optimization

- **Database Sharding**: Partition the metadata database based on `UserID` to distribute the load.
- **Caching Strategy**: 
    - **App-level cache**: Redis/Memcached for user sessions and pre-generated feeds.
    - **CDN**: For serving actual images to the end-user.
- **Data Deduplication**: Hash of the image content to ensure the same image isn't stored multiple times.

---

## 6. Real-World Tech Stack
Instagram famously uses:
- **Django/Python**: Application Servers.
- **PostgreSQL**: Metadata Storage.
- **Redis**: Feed storage and caching.
- **Memcached**: Metadata caching.
- **Cassandra**: For storage of some high-volume metrics.

---

## 7. Reference Links
- [Instagram Engineering Blog](https://instagram-engineering.com/)
- [High Scalability: Instagram Architecture](http://highscalability.com/blog/2011/12/6/instagram-architecture-14-million-users-terabytes-of-photos.html)
- [System Design Primer: Instagram](https://github.com/donnemartin/system-design-primer/blob/master/solutions/system_design/instagram/README.md)
