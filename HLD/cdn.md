# Content Delivery Network (CDN)

A CDN is a geographically distributed group of servers which work together to provide fast delivery of Internet content.

## Real-Time Use Case: Disney+ / Netflix
When a user in India watches a show, the video isn't streamed from a server in the USA. Instead, it's served from a CDN "Edge Server" located in a nearby city (like Mumbai or Chennai). This significantly reduces latency and prevents the main server from being overwhelmed.

## Advanced Concepts
### 1. Push vs. Pull CDNs
- **Push CDN**: Content is pushed to the edge servers by the origin server. Good for static content that rarely changes.
- **Pull CDN**: Edge servers "pull" content from the origin when a user requests it for the first time. More common and easier to manage.
### 2. Edge Computing (Lambda@Edge)
Modern CDNs (like CloudFront or Cloudflare) allow running small snippets of code at the edge. This can be used for custom authentication, A/B testing, or modifying headers before a request reaches the origin.
### 3. Cache Invalidation
The process of clearing cached content when the original file changes. Techniques include **Purging** (manual clear) or **Busting** (using version numbers in the URL like `script.v2.js`).

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Drastic Latency Reduction**: Objects are served from the closest location. | **Cost**: CDNs add an ongoing operational cost based on bandwidth usage. |
| **Increased Availability**: If the origin server goes down, the CDN can still serve cached versions. | **Complexity during Deployment**: Changes might not reflect immediately due to caching. |
| **Security**: Provides built-in protection against DDoS and bot attacks. | **Stale Data**: Users might see old content if the cache TTL (Time To Live) is too high. |

## When to use?
Always use a CDN for static assets (Images, JS, CSS) and large files (Videos) for any application expecting global or high-volume traffic.
