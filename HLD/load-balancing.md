# Load Balancing

Load balancing is the process of distributing network traffic across multiple servers. This ensures no single server bears too much demand. By spreading the work evenly, load balancing improves application responsiveness and increases availability of applications or websites for users.

## Real-Time Use Case
Imagine a high-traffic website like **Amazon** during a Black Friday sale. Millions of users are accessing the site simultaneously. 
- Without a load balancer, a single server would crash under the load.
- With a load balancer (like AWS ELB or Nginx), incoming requests are distributed across hundreds of server instances. If one server goes down, the load balancer redirects traffic to the remaining healthy servers.

## Types of Load Balancing Algorithms
1. **Round Robin**: Requests are distributed sequentially.
2. **Least Connections**: Sends traffic to the server with the fewest active connections.
3. **IP Hash**: The client's IP address determines which server receives the request.

## Example: Nginx Load Balancer Configuration

```nginx
http {
    upstream my_app {
        # Round Robin is default
        server server1.example.com;
        server server2.example.com;
        server server3.example.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://my_app;
        }
    }
}
```

## Key Benefits
- **Scalability**: Add or remove servers seamlessly.
- **Redundancy**: Provides failover capabilities.
- **Efficiency**: Optimizes resource utilization.
