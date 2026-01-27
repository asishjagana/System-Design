# Webhooks (Communication Pattern)

Webhooks are "User-defined HTTP callbacks" that allow one system to provide real-time data to another system when a specific event occurs. This is often called the **"Push"** model of APIs.

## 1. How it Works (The Reverse API)
In a traditional API (Polling/Pull), you ask the server for data. In a Webhook (Push), the server notifies you when it has something.

### The Lifecycle
1. **Subscription**: Client provides a **Webhook URL** to the Server (Provider).
2. **Event Occurs**: An event (e.g., "Payment Success") happens on the Server.
3. **Payload Construction**: The Server creates a JSON payload with event details.
4. **The Callback**: The Server sends an HTTP POST request to the Client's Webhook URL.
5. **Response**: The Client responds with `200 OK` to acknowledge receipt.

---

## 2. Webhooks vs. Polling

- **Polling (Pull)**: Like checking your mailbox every hour to see if a letter arrived.
- **Webhooks (Push)**: Like the mailman ringing your doorbell exactly when a letter arrives.

| Feature | Polling | Webhooks |
| :--- | :--- | :--- |
| **Effort** | Client asks repeatedly. | Server sends once. |
| **Real-time** | Delayed by polling interval. | Instant (as soon as event happens). |
| **Efficiency** | Inefficient (many empty requests). | Highly efficient (only sent on events). |
| **Infrastructure**| Simple client logic. | Requires a publicly accessible server endpoint. |

---

## 3. Real-Time Use Cases
- **Payment Processing**: Stripe notifying your server that a subscription was renewed.
- **GitHub/GitLab**: Triggering a build (CI/CD) when code is pushed.
- **Shopify**: Updating your local database when a customer places an order.
- **Messaging**: Notifying your server when a message is received in a third-party app (e.g., Twilio).
- **IoT Events**: A sensor pushing data when a threshold is crossed.

---

## 4. Implementation in .NET (Webhook Receiver)

To receive webhooks, you must expose an API endpoint that the provider can call.

### A. The Receiver Controller (ASP.NET Core)

```csharp
[ApiController]
[Route("api/webhooks")]
public class PaymentWebhookController : ControllerBase
{
    private const string WebhookSecret = "your_secret_from_stripe";

    [HttpPost("stripe-payment")]
    public async Task<IActionResult> HandlePaymentSuccess()
    {
        // 1. Read the JSON body
        var json = await new StreamReader(HttpContext.Request.Body).ReadToEndAsync();

        // 2. Validate Signature (Crucial for Security!)
        if (!VerifySignature(json, HttpContext.Request.Headers["X-Stripe-Signature"]))
        {
            return BadRequest("Invalid Signature");
        }

        // 3. Process the event
        var paymentEvent = JsonSerializer.Deserialize<PaymentEvent>(json);
        
        // Log or update database
        Console.WriteLine($"Payment received for amount: {paymentEvent.Amount}");

        // 4. Return 200 OK quickly
        return Ok();
    }

    private bool VerifySignature(string payload, string signature) 
    {
        // In a real app, use the Provider's SDK to verify the HMAC signature
        // e.g., Stripe.EventUtility.ConstructEvent(json, sig, secret);
        return true; 
    }
}
```

---

## 5. Security Best Practices

Since your webhook endpoint is public, it's vulnerable to malicious requests.
- **Signatures**: The provider should sign the payload using a secret (HMAC). Your server must verify this signature.
- **Idempotency**: Sometimes the provider might send the same webhook twice (due to network retries). Ensure your code can handle the same event ID twice without side effects.
- **Timeouts**: Process webhooks asynchronously. Don't perform heavy DB work inside the request; just save and return `200 OK` immediately.

---

## 6. Pros and Cons

| Pros | Cons |
| :--- | :--- |
| **Instant Updates**: Data is received the moment the event occurs. | **Public Exposure**: You must expose an endpoint to the internet. |
| **Bandwidth Efficiency**: No wasted requests like polling. | **Delivery Failures**: If your server is down, the event might be missed (though most providers retry). |
| **Scalability**: Your server only does work when there's actually something to do. | **Debugging**: Harder to debug than standard APIs as the source is external. |
