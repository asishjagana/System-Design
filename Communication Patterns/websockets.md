# WebSockets (Communication Pattern)

WebSockets provide a full-duplex, persistent communication channel over a single TCP connection. Unlike HTTP, which is request-response based, WebSockets allow both the client and the server to send data at any time.

## 1. How it Works (The Handshake)
WebSockets start as a standard HTTP request. The client sends a special "Upgrade" header to the server. If the server supports WebSockets, it responds with an `HTTP 101 Switching Protocols` status.

### The Lifecycle
1. **Handshake**: Client sends `Upgrade: websocket`.
2. **Switching**: Server responds with `101 Switching Protocols`.
3. **Open**: A persistent bidirectional TCP connection is established.
4. **Data Transfer**: Frames of data are sent back and forth asynchronously.
5. **Close**: Either side can close the connection.

---

## 2. Why WebSockets? (Real-Time vs. Polling)
- **Bidirectional**: Server can push data without waiting for a client request.
- **Low Latency**: No overhead of creating a new HTTP connection for every message.
- **Efficient**: Minimal header overhead after the initial handshake.

---

## 3. Real-Time Use Cases
- **Instant Messaging**: WhatsApp, Slack, Discord.
- **Live Sports/Financial Feeds**: Real-time scores or stock price updates.
- **Collaborative Editing**: Multiple people editing a document (Google Docs, Figma).
- **Multiplayer Gaming**: Low-latency interaction between players.
- **Live Notifications**: Immediate alerts (e.g., "Someone followed you").

---

## 4. Implementation in .NET (SignalR)

In the .NET ecosystem, **SignalR** is the preferred library. It handles the low-level WebSocket complexity and provides a "Fallback" mechanism (e.g., automatically using Long Polling if WebSockets aren't available).

### A. Backend - The Hub (ASP.NET Core)
A "Hub" is a high-level pipeline that allows your client and server to call methods on each other.

```csharp
using Microsoft.AspNetCore.SignalR;

public class ChatHub : Hub
{
    // Method called by the client
    public async Task SendMessage(string user, string message)
    {
        // Broadcast message to ALL connected clients
        await Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}

// Program.cs setup
// builder.Services.AddSignalR();
// app.MapHub<ChatHub>("/chathub");
```

### B. Client - JavaScript/TypeScript Implementation
Using the `@microsoft/signalr` library.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .build();

// Define handler for server-to-client calls
connection.on("ReceiveMessage", (user, message) => {
    console.log(`${user}: ${message}`);
});

async function start() {
    try {
        await connection.start();
        console.log("Connected to SignalR Hub!");
    } catch (err) {
        console.log(err);
        setTimeout(start, 5000); // Retry logic
    }
}

// Send message to server
async function sendMessage(user, msg) {
    await connection.invoke("SendMessage", user, msg);
}

start();
```

---

## 5. Pros and Cons

| Pros | Cons |
| :--- | :--- |
| **Truly Real-Time**: Sub-millisecond latency once connected. | **Stateful**: The server must keep track of every open connection (uses memory). |
| **Efficient**: No need for headers in every message after the handshake. | **Load Balancing**: Requires "Sticky Sessions" or a backplane (like Redis) for scaling across multiple servers. |
| **Push Capability**: Perfect for high-frequency server-to-client updates. | **Firewalls/Proxies**: Some older corporate firewalls might block "Switching Protocols". |

---

## 6. Scaling WebSockets
Because a WebSocket is a persistent connection to a *specific* server, if you have multiple servers, Server A doesn't know about clients connected to Server B.
- **Solution**: Use a **Backplane** (e.g., **Redis**) or **Azure SignalR Service**. When Server A sends a message, it goes to Redis, which broadcasts it to Server B, which then delivers it to its connected clients.
