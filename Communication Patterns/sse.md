# Server-Sent Events (SSE)

Server-Sent Events (SSE) is a server push technology enabling a client to receive automatic updates from a server via an HTTP connection. Unlike WebSockets, SSE is **unidirectional** (server to client only).

## 1. How it Works
SSE uses a single, long-lived HTTP connection. The server keeps the connection open and sends data in a specific format (`text/event-stream`).

### The Lifecycle
1. **Request**: Client sends a standard HTTP request with `Accept: text/event-stream`.
2. **Open**: Server responds with `Content-Type: text/event-stream` and keeps the connection open.
3. **Stream**: Server sends data "events" whenever they occur.
4. **Automatic Reconnection**: Browsers automatically attempt to reconnect if the connection drops.

---

## 2. Comparison: SSE vs. WebSockets vs. Polling

| Feature | Polling | WebSockets | SSE |
| :--- | :--- | :--- | :--- |
| **Direction** | Pull (Client to Server) | Bi-directional | Push (Server to Client) |
| **Protocol** | HTTP | WebSocket (Binary/Text) | HTTP (Text) |
| **Efficiency** | Low (Many requests) | High | High |
| **Reconnection** | N/A | Manual (must code it) | Automatic (browser handled) |
| **Complexity** | Simple | High | Medium |

---

## 3. Real-Time Use Cases
- **Stock Market Dashboards**: Streaming price updates to a UI.
- **Social Media Notifications**: Pushing "New Like" or "New Comment" alerts.
- **Progress Tracking**: Showing the progress of a server-side build or upload.
- **Live News Feeds**: Streaming headlines as they happen.
- **Server Monitoring**: Streaming CPU/RAM metrics to a dashboard.

---

## 4. Implementation in .NET

### A. Backend - The SSE Endpoint (ASP.NET Core)

```csharp
[HttpGet("stream-updates")]
public async Task GetUpdates(CancellationToken cancellationToken)
{
    Response.Headers.Add("Content-Type", "text/event-stream");

    while (!cancellationToken.IsCancellationRequested)
    {
        // 1. Get your data
        var message = $"Current Time: {DateTime.Now}";

        // 2. Format as per SSE standard: "data: <content>\n\n"
        await Response.WriteAsync($"data: {message}\n\n");
        await Response.Body.FlushAsync();

        // 3. Wait for next update
        await Task.Delay(2000); 
    }
}
```

### B. Client - JavaScript Implementation
Using the built-in `EventSource` API (no external libraries needed).

```javascript
const eventSource = new EventSource('/api/updates/stream-updates');

// Generic message handler
eventSource.onmessage = (event) => {
    console.log("New update:", event.data);
    const element = document.getElementById('updates-list');
    element.innerHTML += `<li>${event.data}</li>`;
};

// Error handling (Automatic reconnection happens behind the scenes)
eventSource.onerror = (error) => {
    console.error("EventSource failed:", error);
};

// Close connection if needed
// eventSource.close();
```

---

## 5. Pros and Cons

| Pros | Cons |
| :--- | :--- |
| **Simple HTTP**: Works over standard HTTP/HTTPS; no special server setup needed. | **Unidirectional**: You cannot send data from client to server over the same connection. |
| **Auto-Reconnect**: Browsers handle connection drops and retries out of the box. | **Connection Limits**: Browsers often limit the number of open SSE connections per domain (usually 6). |
| **Efficient**: No binary overhead; uses plain text. | **Text Only**: Only supports UTF-8 text (Binary data must be Base64 encoded). |
| **Lightweight**: Lower server resources than full-duplex WebSockets. | |

---

## 6. When to use SSE?
If you only need to **push data from the server** (e.g., status updates, dashboards, feeds) and don't need the client to send data back over the same channel, SSE is a much simpler and more efficient choice than WebSockets.
