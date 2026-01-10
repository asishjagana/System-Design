# Circuit Breaker Pattern

In a microservices environment, if one service is slow or failing, it can cause a "cascading failure" by holding up threads in other services. The Circuit Breaker prevents this.

## Real-Time Use Case: E-commerce Checkout
The **Checkout Service** calls the **Payment Service**. If the Payment Service is down, the Checkout Service shouldn't just keep trying and wait for timeouts (blocking its own threads).
The **Circuit Breaker** "Trips" (opens) and immediately returns an error or a "Service Temporarily Unavailable" message to the user, allowing the system to fail fast and recover.

## Advanced Concepts: The Three States
1. **Closed**: Everything is normal. Requests flow through.
2. **Open**: Service is failing consistently. Requests are cut off immediately.
3. **Half-Open**: After a timeout, it allows a few "test" requests. If they succeed, it closes. If they fail, it goes back to Open.

## Example Code (C# using Polly)

```csharp
var circuitBreakerPolicy = Policy
    .Handle<HttpRequestException>()
    .CircuitBreaker(
        exceptionsAllowedBeforeBreaking: 3,
        durationOfBreak: TimeSpan.FromSeconds(30)
    );

// Usage
circuitBreakerPolicy.Execute(() => {
    // Call the external service
    return CallPaymentApi();
});
```

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Resilience**: Prevents a single failing service from taking down the whole system. | **Complex to Tune**: Choosing the right thresholds (e.g., 3 failures in 10 seconds) is tricky. |
| **Fail Fast**: Gives the failing service time to recover by stopping the bombardment of requests. | **Monitoring Overhead**: Requires advanced observability to know when a circuit is open. |
| **Fallback Options**: Can be paired with a "fallback" (e.g., "Use cached data if service is down"). | |

## When to use?
Wherever a service makes remote calls to another service or database across a network, especially if that dependency is unreliable or subject to high latency.
