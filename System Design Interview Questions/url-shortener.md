# Design a URL Shortener (TinyURL)

How would you design a service like TinyURL that generates short aliases for long URLs?

## 1. Functional Requirements
- **Shortening**: Given a long URL, return a shorter unique alias.
- **Redirection**: Given a short alias, redirect the user to the original URL.
- **Custom Aliases**: (Optional) Allow users to pick their own alias.
- **Expiration**: (Optional) Links should expire after a certain time.

## 2. Capacity Estimation
- **Traffic**: 100M new URLs per month.
- **Read/Write Ratio**: 10:1 (Heavy read).
- **Storage**: 100M * 5 years * 500 bytes per URL ≈ 30 Terabytes.

## 3. Storage Layer
We need a simple Mapping Table:
- `ShortURL` (PK) - e.g., `8d2g3k`
- `LongURL` - e.g., `https://google.com/search?q=system+design`
- `CreatedAt`
- `ExpirationDate`

**Which Database?** NoSQL (like MongoDB or Cassandra) or a simple Key-Value store (Redis) for high performance. A relational database with indexing is also fine for start.

## 4. Shortening Logic (The "Magic")
How to generate `8d2g3k`?
- **Hashing (MD5/SHA256)**: Take hash of URL and take first 6-8 chars. Conflict risk exists.
- **Base62 Encoding**: Convert an auto-incrementing ID (from DB) to Base62 (0-9, a-z, A-Z).
  - ID 10,001 -> `s7G`
  - This guarantees uniqueness.

## 5. High-Level Architecture
1. **Client** sends long URL.
2. **Load Balancer** distributes request.
3. **App Server** generates Short Code (using a Key Generation Service or Base10 to Base62 conversion).
4. **Database** stores the mapping.
5. **Cache** (Redis) stores frequently accessed mappings for fast redirection.

## 6. Real-Time Use Case
**Bit.ly** or **Twitter (t.co)** uses this to save space in posts and track click analytics.
