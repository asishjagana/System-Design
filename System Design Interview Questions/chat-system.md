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

## 5. Real-Time Use Case
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
