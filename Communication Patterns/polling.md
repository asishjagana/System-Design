# Polling (Communication Pattern)

Polling is a client-side communication technique where the client repeatedly requests data from a server at regular intervals.

## 1. How it Works
In a polling setup, the client (browser, mobile app, or another service) sends an HTTP request to the server, and the server responds immediately with the current state of the data. If there is no new data, the server returns an empty or "no-change" response. The client then waits for a specific duration (e.g., 5 seconds) before sending the next request.

### Basic Workflow
1. **Client**: "Is the task finished?" (HTTP GET)
2. **Server**: "No, still in progress." (HTTP 200)
3. **Client**: (Waits 5 seconds...)
4. **Client**: "Is the task finished?" (HTTP GET)
5. **Server**: "Yes, here is the result." (HTTP 200 + Data)

---

## 2. Short Polling vs. Long Polling

### Short Polling (Standard Polling)
The client sends a request, and the server responds immediately. This is simple to implement but can be inefficient as it generates many empty HTTP requests.
- **Good for**: Scenarios where data changes very frequently (e.g., stock tickers).

### Long Polling
The client sends a request, and the server **holds the request open** until new data is available or a timeout occurs. Once data is returned, the client immediately sends the next request.
- **Good for**: Real-time notifications or chat where updates are sporadic.

---

## 3. Real-Time Use Cases
- **Task Status tracking**: Checking the progress of a long-running background job (e.g., file conversion, report generation).
- **Legacy Systems**: When modern protocols like WebSockets aren't supported.
- **Health Monitoring**: Periodically checking if a server or microservice is alive.
- **Order Status**: Checking if a food delivery order has been picked up by a driver.

---

## 4. Implementation in .NET

### A. Backend - The Pollable API (ASP.NET Core)
This endpoint simulates a long-running task.

```csharp
[ApiController]
[Route("api/[controller]")]
public class TaskController : ControllerBase
{
    private static readonly Dictionary<string, string> _taskProgress = new();

    [HttpPost("start")]
    public IActionResult StartTask([FromBody] string taskId)
    {
        _taskProgress[taskId] = "In Progress";
        // Simulate background work...
        return Ok(new { TaskId = taskId });
    }

    [HttpGet("status/{taskId}")]
    public IActionResult GetStatus(string taskId)
    {
        if (!_taskProgress.ContainsKey(taskId)) return NotFound();
        
        var status = _taskProgress[taskId];
        return Ok(new { TaskId = taskId, Status = status });
    }
}
```

### B. Client - The Polling Loop (C# Console/Worker Service)
Using `HttpClient` to periodically check the status.

```csharp
using System.Net.Http.Json;

public class PollingClient
{
    private readonly HttpClient _httpClient = new HttpClient();

    public async Task PollTaskStatus(string taskId)
    {
        bool isCompleted = false;
        
        while (!isCompleted)
        {
            Console.WriteLine("Polling server for status...");
            
            var response = await _httpClient.GetFromJsonAsync<TaskResponse>($"https://localhost:5001/api/task/status/{taskId}");

            if (response?.Status == "Completed")
            {
                Console.WriteLine("Task finished successfully!");
                isCompleted = true;
            }
            else
            {
                // Wait for 5 seconds before next poll
                await Task.Delay(5000);
            }
        }
    }

    public record TaskResponse(string TaskId, string Status);
}
```

---

## 5. Pros and Cons

| Pros | Cons |
| :--- | :--- |
| **Simplicity**: No need for persistent connections or complex server state (unlike WebSockets). | **High Overhead**: Frequent HTTP requests consume bandwidth and CPU on both sides. |
| **Firewall Friendly**: Uses standard HTTP/HTTPS ports (80/443). | **Latency**: Updates are only as fast as the polling interval (e.g., if data changes right after a poll, you wait 5s to see it). |
| **Robust**: Works even in unstable network conditions where persistent connections might drop. | **Server Load**: Scaling is difficult if thousands of clients are hitting the server every few seconds. |

---

## 6. When to avoid Polling?
If your application requires **sub-second real-time** updates (e.g., high-frequency trading or multiplayer games), polling is usually the wrong choice. In those cases, use **WebSockets** or **Server-Sent Events (SSE)**.
