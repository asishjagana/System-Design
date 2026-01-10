# Load Balancing

Load balancing is the process of distributing network traffic across multiple servers. This ensures no single server bears too much demand. By spreading the work evenly, load balancing improves application responsiveness and increases availability of applications or websites for users.

## Real-Time Use Case
Imagine a high-traffic website like **Amazon** during a Black Friday sale. Millions of users are accessing the site simultaneously. 
- Without a load balancer, a single server would crash under the load.
- With a load balancer (like AWS ELB or Nginx), incoming requests are distributed across hundreds of server instances. If one server goes down, the load balancer redirects traffic to the remaining healthy servers.

## Advanced Concepts
### 1. Layer 4 vs Layer 7 Load Balancing
- **Layer 4 (Transport Layer)**: Works at the TCP/UDP level. It makes routing decisions based on IP address and port. It's extremely fast because it doesn't look at the packet content.
- **Layer 7 (Application Layer)**: Works at the HTTP level. It can route traffic based on URL, Headers, or Cookies (Sticky Sessions). It's more CPU-intensive but much smarter.

### 2. Global Server Load Balancing (GSLB)
Distributes traffic across multiple data centers in different geographic regions. If a whole data center in Virginia fails, GSLB sends traffic to the California data center.

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **High Availability**: Automatically detects and bypasses failed servers. | **Single Point of Failure**: If the Load Balancer itself goes down, the system is inaccessible (Solution: use High Availability LB pairs). |
| **Security**: Can act as a barrier against DDoS attacks. | **Complexity**: Adds another layer of configuration and maintenance. |
| **SSL Offloading**: The LB can handle SSL encryption/decryption, freeing up resources on the app servers. | **Latency**: Adds a tiny bit of latency to every request. |

## Key Benefits
- **Scalability**: Add or remove servers seamlessly.
- **Redundancy**: Provides failover capabilities.
- **Efficiency**: Optimizes resource utilization.
