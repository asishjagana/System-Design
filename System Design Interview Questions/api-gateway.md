# Design an API Gateway

An API Gateway is a server that is the single entry point into the system. It is similar to the Facade pattern from object-oriented design.

## 1. Requirements
- **Routing**: Forward requests to the correct microservice.
- **Authentication/Authorization**: Verify JWT tokens or API keys.
- **Rate Limiting**: Prevent DDoS attacks and abuse (e.g., max 100 requests per minute).
- **Protocol Translation**: Convert between REST (external) and gRPC (internal).
- **Monitoring & Logging**: Track every request for analytics.

## 2. Real-Time Use Case: Amazon's API Gateway
AWS provides a managed service that handles millions of concurrent API calls. It manages traffic, authorize users, and provide metrics for all back-end services.

## 3. High-Level Architecture
1. **Client** (Mobile/Web) sends a request.
2. **Load Balancer** distributes to one of the API Gateway instances.
3. **API Gateway Middleware**:
    - **Auth Service**: Checks if the user is logged in.
    - **Rate Limiter**: Checks if the user has exceeded their quota (often using Redis).
    - **Service Discovery**: Finds the IP of the target microservice (e.g., via Consul or Eureka).
4. **Target Service** receives the request and processes it.

## 4. Implementation Detail: Rate Limiting
Common algorithms:
- **Token Bucket**: A bucket holds tokens. Each request takes a token. Tokens refill over time.
- **Leaky Bucket**: Requests enter at any rate, but leave at a fixed rate.
- **Fixed Window**: 100 requests per calendar minute. (Prone to spikes at the end of the window).

## 5. Pros and Cons of using an API Gateway
| Pros | Cons |
| :--- | :--- |
| **Security**: Centralized point to enforce security policies. | **Single Point of Failure**: If the Gateway is down, the whole system is down. |
| **Simplifies Clients**: Clients don't need to know about 50 microservices. | **Bottleneck**: Can become a performance bottleneck if not scaled correctly. |
| **Observability**: One place to log all incoming traffic. | **Complexity**: Adds another layer of infrastructure to maintain. |

## 6. Real-time implementation concept (Node.js/Express)
```javascript
const express = require('express');
const axios = require('axios');
const app = express();

app.use('/orders', async (req, res) => {
    // 1. Auth check
    if (!checkAuth(req)) return res.status(401).send();
    
    // 2. Routing
    const response = await axios.get('http://orders-service:3000' + req.url);
    res.json(response.data);
});

app.listen(8080);
```
