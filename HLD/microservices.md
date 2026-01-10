# Microservices Architecture

Microservices is an architectural style that structures an application as a collection of services that are highly maintainable, testable, loosely coupled, and independently deployable.

## Real-Time Use Case: Netflix
Netflix transitioned from a monolithic architecture to microservices. Each feature—like user recommendations, billing, video streaming, and user profiles—is handled by a separate service. This allows them to update the "Recommendation" logic without touching the "Billing" system.

## Advanced Concepts
### 1. API Gateway
A single entry point for all clients. It handles cross-cutting concerns like authentication, rate limiting, and request routing.
### 2. Service Mesh (Example: Istio)
A dedicated infrastructure layer for handling service-to-service communication, providing features like traffic management, security, and observability without modifying the application code.
### 3. Saga Pattern
Handles distributed transactions. Since each microservice has its own database, you can't use traditional ACID transactions. A Saga is a sequence of local transactions where each one updates the database and triggers the next step. If one fails, "compensating transactions" are run to undo the previous steps.

## Example: Communication via gRPC (C#)

```csharp
// Simple gRPC Service Definition
public class OrderService : OrderProcessor.OrderProcessorBase {
    public override async Task<OrderResponse> CreateOrder(OrderRequest request, ServerCallContext context) {
        // Logic to save order
        return new OrderResponse { OrderId = "123", Status = "Success" };
    }
}
```

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Independent Deployment**: Update one service without redeploying the whole app. | **Operational Complexity**: Managing 100s of services requires robust DevOps/SRE. |
| **Technology Diversity**: Use Python for Data Science and Go for high-performance networking. | **Network Latency**: Frequent inter-service communication adds overhead. |
| **Scalability**: Scale only the "hot" services (e.g., Search during a sale). | **Data Consistency**: Hard to maintain consistency across distributed databases. |
| **Fault Isolation**: One service crashing doesn't necessarily bring down the entire system. | **Testing Difficulty**: End-to-end testing becomes much more complex. |

## When to use?
Use when the application is large, has a high number of developers, and requires rapid, independent feature delivery. Avoid for small, simple projects where a monolith is faster to build and maintain.
