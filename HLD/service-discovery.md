# Service Discovery

In a microservices environment, service instances are dynamic. They scale up/down, crash, or move to different nodes. Service Discovery is how services find each other's network locations (IP and Port).

## Real-Time Use Case: Netflix / Airbnb
Netflix uses **Eureka** (Service Discovery) and **Ribbon** (Client-side Load Balancing). When the "Video Service" needs to call the "Recommendation Service", it doesn't use a hardcoded IP. It asks Eureka: "Where are the Recommendation Services?", gets a list of IPs, and picks one.

## Advanced Concepts
### 1. Client-Side Discovery
The client is responsible for determining the network locations of available service instances and load balancing requests across them. The client queries a **Service Registry**.
- **Pros**: Client knows exactly what's available; less network hops.
- **Cons**: Client logic becomes complex; coupling between client and registry.

### 2. Server-Side Discovery (API Gateway/Proxy)
The client makes a request to a service via a load balancer. The load balancer queries the service registry and routes the request to an available instance.
- **Pros**: Client is simple; decoupling.
- **Cons**: More network hops; load balancer must be highly available.

### 3. Service Registry (The Heart)
A database containing the network locations of service instances. Examples: **Consul**, **Etcd**, **Zookeeper**.

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Automation**: No manual updates to config files when services move. | **Complexity**: Introduces a new piece of infrastructure to manage. |
| **Resilience**: Automatically removes unhealthy instances via "Heartbeats". | **Consistency Issues**: If the registry is down or lagging, services can't find each other. |
| **Scalability**: Enables effortless horizontal scaling of services. | |

## When to use?
Essential for any microservices architecture where you have multiple instances of services running in an elastic environment (Cloud/Kubernetes).
