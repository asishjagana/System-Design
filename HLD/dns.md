# Domain Name System (DNS)

DNS is the "Phonebook of the Internet." It translates human-readable domain names (e.g., `google.com`) into machine-readable IP addresses (e.g., `142.250.190.46`).

---

## 1. How a Domain is Mapped to an IP (The Resolution Process)

When you type a URL into your browser, several steps happen to find the IP address:

1.  **Browser & OS Cache**: The browser first checks its own cache. If not found, it asks the Operating System, which checks the `hosts` file and its own DNS cache.
2.  **DNS Resolver (Recursive)**: If the IP isn't found locally, a request is sent to a Recursive Resolver (usually provided by your ISP or a public provider like Google/Cloudflare).
3.  **Root Name Server**: The resolver asks a Root server. Root servers don't know the IP, but they know where the **TLD (Top-Level Domain)** servers are (e.g., for `.com`).
4.  **TLD Name Server**: The root server directs the resolver to the TLD server. The TLD server knows where the **Authoritative Name Servers** for that specific domain are.
5.  **Authoritative Name Server (ANS)**: This is the final step. The ANS holds the actual DNS records. It returns the IP address for the requested domain to the resolver.
6.  **Caching**: The resolver sends the IP back to the browser and caches it for future use (based on the TTL).

---

## 2. Records in Authoritative Name Servers (ANS)

Authoritative servers store various types of records to manage traffic and identity:

- **A Record**: Maps a domain name to an **IPv4** address.
- **AAAA Record**: Maps a domain name to an **IPv6** address.
- **CNAME (Canonical Name)**: Maps an alias name to another domain name (e.g., `www.example.com` -> `example.com`).
- **MX (Mail Exchange)**: Specifies the mail servers responsible for receiving email for the domain.
- **TXT Record**: Allows administrators to insert arbitrary text. Often used for security (SPF, DKIM, DMARC) and domain ownership verification.
- **NS (Name Server)**: Specifies which servers are authoritative for a domain or subdomain.
- **SOA (Start of Authority)**: Information about the DNS zone, including the primary name server, administrator email, and serial number for replication.

---

## 3. Types of DNS Routing

Advanced DNS services (like AWS Route 53 or Cloudflare) allow you to route traffic based on various policies:

- **Simple Routing**: A single record that points to one or more IP addresses (chosen randomly).
- **Weighted Round Robin**: Traffic is distributed across multiple resources in specific proportions (e.g., 70% to Server A, 30% to Server B). Great for A/B testing or canary releases.
- **Latency-Based Routing**: Processes requests based on the lowest network latency for your users (i.e., which data center is "faster" for them).
- **Geo-location Routing**: Routes traffic based on the physical location of your users (e.g., users in Europe go to a Dublin data center).
- **Geo-proximity Routing**: Routes traffic based on the geographic location of your resources and can optionally shift traffic from resources in one location to resources in another.
- **Failover Routing**: Used for disaster recovery. It routes traffic to a primary resource as long as it's healthy and switches to a secondary resource if the primary fails.
- **Multivalue Answer Routing**: Allows DNS to return up to eight healthy records selected at random.

### How to Configure?
Configuration is typically done through a DNS provider's dashboard or API. You define "Hosted Zones," then create "Record Sets" (like an A record for `api.yourdomain.com`) and select the "Routing Policy" (e.g., Latency vs Failover).

---

## 4. Why Public DNS? (Google, Cloudflare, ISPs)

You might wonder why companies like Google (`8.8.8.8`) and Cloudflare (`1.1.1.1`) provide free DNS servers:

- **Performance & Speed**: These providers use **Anycast** technology to route your DNS queries to the physically closest data center, making lookups much faster than many local ISP DNS servers.
- **Security**: They often include built-in protection against phishing, malware, and cache poisoning attacks.
- **Privacy & Transparency**: Public providers often have stricter logging policies than some ISPs, promising not to sell your browsing data.
- **Reliability**: They have massive infrastructure that is much less likely to go down compared to a small ISP's DNS resolver.
- **Data Collection (The Trade-off)**: While they offer speed/security, providing DNS gives these companies high-level insights into global internet traffic patterns (though they usually anonymize this data).

---

## 5. Summary Table

| Feature | Description |
| :--- | :--- |
| **Recursive Resolver** | The middleman that does the hard work of searching. |
| **Authoritative Server** | The final holder of the official records. |
| **TTL (Time To Live)** | How long a record stays in a cache before expiring. |
| **Anycast** | Routing one IP to multiple locations for speed and redundancy. |
