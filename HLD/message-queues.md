# Message Queues

A message queue provides an asynchronous communications protocol, meaning that the sender and receiver of the message do not need to interact with the message queue at the same time.

## Real-Time Use Case
**Uber Order Processing**: When you book a ride:
1. The App sends a "Ride Requested" message to a Queue.
2. The user gets an immediate "Looking for drivers" response.
3. A background Worker (Consumer) picks up the message and starts the complex logic of matching you with a driver, calculating surge pricing, and notifying nearby drivers.
This prevents the user from waiting for the matching logic to finish before seeing the "Requesting" screen.

## Popular Tools
- **RabbitMQ**: Great for complex routing.
- **Apache Kafka**: High throughput, log-based streaming.
- **Amazon SQS**: Managed queue service.

## Example: Producer in C# (using RabbitMQ)

```csharp
using RabbitMQ.Client;
using System.Text;

public class MessageProducer
{
    public void SendMessage(string message)
    {
        var factory = new ConnectionFactory() { HostName = "localhost" };
        using var connection = factory.CreateConnection();
        using var channel = connection.CreateModel();

        channel.QueueDeclare(queue: "task_queue", durable: true, exclusive: false, autoDelete: false);

        var body = Encoding.UTF8.GetBytes(message);

        channel.BasicPublish(exchange: "", routingKey: "task_queue", basicProperties: null, body: body);
        Console.WriteLine($" [x] Sent {message}");
    }
}
```

## Key Benefits
- **Decoupling**: Services don't need to know about each other.
- **Scalability**: You can add more consumers to handle high volume.
- **Resilience**: If a consumer fails, messages stay in the queue until retried.
