# Content Delivery Network (CDN)

A **Content Delivery Network (CDN)** is a globally distributed network of proxy servers that serve content from locations physically closer to the user. 

---

## 1. How it Works
When a user requests a file (like an image or video), the request is routed via DNS to the closest CDN edge server. If the server has the file, it returns it instantly. If not, it fetches it from the origin server, caches it, and then delivers it.

### Serving content from CDNs improves performance in two ways:
- **Low Latency**: Users receive content from data centers close to them.
- **Offloading Origin**: Your primary servers don't have to serve requests that the CDN fulfills.

---

## 2. Push CDNs
In a **Push CDN**, the origin server takes full responsibility for sending new or updated content to the CDN.

- **Mechanism**: You upload content directly to the CDN and rewrite your application's URLs to point to the CDN's domain.
- **Best Use Case**: 
    - Sites with small traffic.
    - Sites with content that is rarely updated.
- **Pros**: Minimized traffic to origin; content is only uploaded when it changes.
- **Cons**: You must manually manage the lifecycle of every file on the CDN.

---

## 3. Pull CDNs
A **Pull CDN** automatically grabs new content from your origin server when the first user requests it.

- **Mechanism**: You leave the content on your origin server and simply rewrite URLs to point to the CDN. The CDN "pulls" the file if it's missing from its cache.
- **TTL (Time To Live)**: A value that determines how long content remains cached on the CDN before it's considered "stale" and must be re-pulled.
- **Best Use Case**: 
    - Sites with heavy traffic.
    - Dynamic sites where content changes frequently but is requested often.
- **Pros**: Extremely low maintenance; minimizes storage space on the CDN (only popular content is kept).
- **Cons**: The very first request for a file is slow (cache miss).

---

## 4. Disadvantages & Trade-offs

- **Significant Costs**: Depending on traffic volume, bandwidth costs can be high.
- **Stale Content**: If you update a file on your server but the TTL hasn't expired, users will continue to see the old version.
- **URL Complexity**: Requires changing your static asset paths (e.g., `images/logo.png` becomes `https://cdn.example.com/images/logo.png`).

---

## 5. Real-Time Use Case: Streaming & Static Assets
- **Netflix/Disney+**: Use CDNs to store video chunks globally so 4K video doesn't buffer.
- **E-commerce**: Store all product images and CSS on CDNs to ensure the page loads in under a second for a global audience.

---

## 6. Source(s) and Further Reading
- [Globally Distributed Content Delivery (Wikipedia)](https://en.wikipedia.org/wiki/Content_delivery_network)
- [The Differences Between Push and Pull CDNs](https://www.keycdn.com/blog/push-cdn-vs-pull-cdn)
- [Amazon CloudFront Documentation](https://aws.amazon.com/cloudfront/)
