# Reverse Proxy

A **Reverse Proxy** is a web server that centralizes internal services and provides unified interfaces to the public. Requests from clients are forwarded to a server that can fulfill it before the reverse proxy returns the server's response to the client.

## 1. How it Works
Unlike a standard proxy (Forward Proxy) which sits in front of a client to hide their identity, a **Reverse Proxy** sits in front of one or more web servers to hide the identity and structure of the backend architecture.

---

## 2. Key Benefits

### 🛡️ Increased Security
- **Backend Anonymity**: Hides information about backend servers, preventing direct attacks on application nodes.
- **IP Management**: Allows blacklisting of malicious IPs and limiting connections per client to prevent DDoS attacks.

### 📈 Scalability and Flexibility
- **Unified Interface**: Clients only see the reverse proxy's IP. This allows you to add, remove, or change backend server configurations without the client ever knowing.
- **Microservices Routing**: Can route different paths (e.g., `/api` vs `/static`) to different backend services.

### 🔐 SSL Termination
- **Expensive Offloading**: Decrypting incoming HTTPS requests and encrypting responses is CPU-intensive. A reverse proxy handles this so backend servers can focus on application logic.
- **Simplified Management**: Removes the need to install and manage X.509 certificates on every single internal server.

### 💨 Compression and Caching
- **Efficiency**: Can compress large server responses (gzip/brotli) to reduce bandwidth usage.
- **Speed**: Caches responses for common requests, returning them instantly without hitting the backend.

### 🖼️ Static Content Hosting
- Serves static files directly (HTML, CSS, JS, Photos, Videos), which is much faster than passing these requests to an application server like Node.js or Python.

---

## 3. Load Balancer vs. Reverse Proxy

While they share many features, their primary purpose differs:

| Feature | Load Balancer | Reverse Proxy |
| :--- | :--- | :--- |
| **Primary Goal** | Distribute traffic across **multiple** identical servers. | Provide a single interface to **one or more** backend services. |
| **When to use?** | When you have a cluster of servers serving the same app. | Even with a **single** server, to gain security, SSL, and caching. |
| **Layer** | Often Layer 4 (Transport) or Layer 7 (Application). | Typically Layer 7 (Application). |

> [!NOTE]
> Modern solutions like **Nginx** and **HAProxy** are versatile and can perform BOTH roles simultaneously.

---

## 4. Disadvantages and Challenges
- **Increased Complexity**: Adds another layer to configure and maintain in the infrastructure.
- **Single Point of Failure (SPOF)**: If the reverse proxy goes down, the entire site is unreachable. 
- **Mitigation**: Deploy multiple reverse proxies in a **Failover** configuration (Active-Passive or Active-Active).

---

## 5. Popular Solutions
- **NGINX**: The most popular web server/reverse proxy known for performance and static content handling.
- **HAProxy**: A specialized high-performance load balancer and reverse proxy.
- **Apache (mod_proxy)**: A reliable, feature-rich classic.

---

## 6. Source(s) and Further Reading
- [Reverse Proxy vs Load Balancer (NGINX Blog)](https://www.nginx.com/resources/glossary/reverse-proxy-vs-load-balancer/)
- [HAProxy Architecture Guide](https://www.haproxy.org/download/1.2/doc/architecture.txt)
- [Wikipedia: Reverse Proxy](https://en.wikipedia.org/wiki/Reverse_proxy)
