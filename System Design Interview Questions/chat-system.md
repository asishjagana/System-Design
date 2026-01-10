# Design WhatsApp / Chat System

Designing a real-time messaging system like WhatsApp.

## 1. Requirements
- **One-on-one chat**: Single user to single user.
- **Group chat**: Multiple users.
- **Online status**: Last seen / Online.
- **Read Receipts**: Sent, Delivered, Read.
- **Media Support**: Images, Videos.

## 2. Key Challenges
- **Low Latency**: Real-time delivery.
- **High Availability**: System must always be up.
- **Message Ordering**: Messages must appear in the correct sequence.

## 3. High-Level Architecture
- **WebSockets**: Crucial for bi-directional real-time communication. HTTP is request-response only; WebSockets keep a persistent connection.
- **Chat Service**: Handles message routing.
- **Presence Service**: Keeps track of who is online.
- **Database**:
  - **Message Store**: Cassandra or HBase (Handle massive write volume and partitioning by `ChatId`).
  - **User Data**: SQL (PostgreSQL/MySQL).

## 4. Workflow (Message Send)
1. **User A** sends message via WebSocket.
2. **Chat Server** receives it, saves to DB.
3. **Presence Service** checks if **User B** is online.
4. If online, **Chat Server** pushes message to **User B** via their WebSocket.
5. If offline, **Push Notification Service** triggers a mobile notification.

## Advanced Concepts
### 1. Message Storage Strategy
- **Hot Data (Recent Messages)**: Store in an in-memory database like Redis or a high-performance NoSQL like Cassandra sharded by `ChatId`.
- **Cold Data (Archived Messages)**: Move to cheaper storage like S3 or HDFS.

### 2. Synchronization across Devices
If a user is logged into both Phone and Laptop:
- When they send a message from Phone, the Laptop needs to receive a sync update.
- We use a **Sequence ID** for each message in a chat to detect gaps and ensure the UI shows everything in order.

## Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Real-time Experience**: WebSockets provide sub-second delivery. | **Persistent Connections**: Maintaining millions of open WebSockets is memory intensive for servers. |
| **High Reliability**: Messages are persisted before delivery, ensuring no data loss. | **Complex State Management**: Handling "Online/Offline" status accurately across a global cluster is hard. |
| **Rich Media Support**: CDNs make sharing photos and videos seamless. | **Security**: End-to-end encryption (E2EE) makes server-side search and analytics impossible. |

## Real-Time Use Case
**WhatsApp, Slack, Discord**. All use WebSockets or MQTT for efficiency.

## 6. Code Concept (WebSocket Client - JS)

```javascript
const socket = new WebSocket('ws://chat.example.com');

// Send Message
socket.send(JSON.stringify({
    to: 'user_123',
    msg: 'Hello World!'
}));

// Receive Message
socket.onmessage = (event) => {
    const data = JSON.parse(event.data);
    console.log(`New message from ${data.from}: ${data.msg}`);
};
```
