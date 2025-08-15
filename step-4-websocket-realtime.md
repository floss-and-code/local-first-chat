# Step 4: WebSocket Real-time Communication 🔄

Add real-time messaging capabilities with WebSocket server and client, enabling instant message synchronization across all connected users.

**Time:** ~30 minutes  
**Outcome:** Real-time chat with auto-reconnect and presence tracking

## Prerequisites

- [ ] Completed [Step 3: PWA & Offline Mode](./step-3-pwa-offline.md)
- [ ] Offline queue working
- [ ] PWA features functional

## What You'll Build

In this step:
- ✅ WebSocket server with Node.js
- ✅ Client-side WebSocket with auto-reconnect
- ✅ Message broadcasting
- ✅ User presence tracking
- ✅ Connection status management

## Part 1: Create WebSocket Server

Build a lightweight WebSocket server:

> **🤖 Continue Prompt:**
> ```
> Create server/server.ts with WebSocket server using ws library:
> - Port 3001 for WebSocket
> - Message compression with lz-string
> - User presence tracking (connected users)
> - Message broadcasting to all clients
> - Admin commands (clear chat, user count)
> - Heartbeat/ping-pong for connection health
> - Batch messages for efficiency
> - Rate limiting (max 10 msgs/second)
> - Connection cleanup on disconnect
> ```

Expected server structure:

```typescript
// server/server.ts
import WebSocket from 'ws';
import LZString from 'lz-string';

interface Client {
  id: string;
  username: string;
  ws: WebSocket;
  isAdmin: boolean;
  lastPing: number;
}

class ChatServer {
  private wss: WebSocket.Server;
  private clients: Map<string, Client> = new Map();
  
  start(): void { /* ... */ }
  broadcast(message: any): void { /* ... */ }
  handleMessage(client: Client, data: string): void { /* ... */ }
}
```

Add server dependencies:

```bash
npm install ws @types/ws
npm install -D nodemon ts-node
```

Update package.json scripts:

> **🤖 Continue Prompt:**
> ```
> Add these scripts to package.json:
> - "server": "nodemon server/server.ts"
> - "dev:all": "concurrently \"npm run dev\" \"npm run server\""
> - Install concurrently for running both
> ```

## Part 2: Create WebSocket Client

Implement robust client-side WebSocket:

> **🤖 Continue Prompt:**
> ```
> Create src/websocket.ts with:
> - Auto-reconnect with exponential backoff
> - Connection state management
> - Message compression/decompression
> - Event emitter pattern for messages
> - Queue integration for offline messages
> - Heartbeat to detect disconnections
> - Methods: connect(), disconnect(), send(), on()
> - Max reconnect attempts: 5
> - Initial reconnect delay: 1000ms
> ```

Client structure:

```typescript
// src/websocket.ts
type ConnectionState = 'connecting' | 'connected' | 'disconnected' | 'error';

class WebSocketClient {
  private ws: WebSocket | null = null;
  private reconnectAttempts = 0;
  private messageQueue: Message[] = [];
  private listeners: Map<string, Function[]> = new Map();
  
  connect(url: string, user: User): void { /* ... */ }
  send(message: any): void { /* ... */ }
  on(event: string, callback: Function): void { /* ... */ }
  private reconnect(): void { /* ... */ }
  private setupHeartbeat(): void { /* ... */ }
}
```

## Part 3: Integrate WebSocket with Chat

Connect WebSocket to your existing chat:

> **🤖 Continue Prompt:**
> ```
> Update src/chat.ts to integrate WebSocket:
> - Initialize WebSocket on login
> - Send messages through WebSocket when online
> - Fall back to offline queue when disconnected
> - Update UI with connection status
> - Handle incoming messages
> - Show user count from server
> - Sync local and remote messages
> - Prevent duplicate messages
> ```

Integration points:
```typescript
// Message flow
async sendMessage(text: string) {
  const message = createMessage(text);
  
  // Save locally first
  await storage.saveMessage(message);
  
  // Try WebSocket, queue if offline
  if (websocket.isConnected()) {
    websocket.send(message);
  } else {
    offlineQueue.enqueue(message);
  }
}
```

## Part 4: Add Presence Features

Show who's online:

> **🤖 Continue Prompt:**
> ```
> Add user presence features:
> - Display active user count
> - Show "User joined" notifications
> - Show "User left" notifications
> - List of online users (optional)
> - Typing indicators (throttled)
> - Last seen timestamps
> - Connection quality indicator
> ```

## Part 5: Implement Message Sync

Synchronize messages across devices:

> **🤖 Continue Prompt:**
> ```
> Create src/sync.ts for message synchronization:
> - Detect message conflicts
> - Merge local and remote messages
> - Handle out-of-order messages
> - Deduplicate by message ID
> - Sync on reconnection
> - Request missed messages
> - Timestamp reconciliation
> ```

Sync strategy:
```typescript
class MessageSync {
  async reconcile(localMessages: Message[], remoteMessages: Message[]): Promise<Message[]> {
    // Merge and deduplicate
    // Sort by timestamp
    // Resolve conflicts
  }
}
```

## Part 6: Add Connection UI

Enhance UI with connection feedback:

> **🤖 Continue Prompt:**
> ```
> Update styles and UI for connection status:
> - Connection indicator (green/yellow/red)
> - Reconnecting spinner
> - User count badge
> - "Reconnecting in X seconds" message
> - Network quality indicator
> - Smooth transitions between states
> ```

Status indicators:
```css
.status-indicator {
  width: 8px;
  height: 8px;
  border-radius: 50%;
}

.status-connected { background: #10b981; }
.status-connecting { background: #f59e0b; animation: pulse 1s infinite; }
.status-disconnected { background: #ef4444; }
```

## Part 7: Test Real-time Features

### Local Testing

1. **Start both servers:**
   ```bash
   npm run dev:all
   ```

2. **Open multiple browser tabs:**
   - Different users in each tab
   - Send messages between tabs
   - Verify instant delivery

3. **Test reconnection:**
   - Stop the WebSocket server
   - UI should show disconnected
   - Restart server
   - Should auto-reconnect

### Network Testing

> **🤖 Continue Prompt:**
> ```
> Add network simulation to test resilience:
> - Simulate high latency
> - Test with packet loss
> - Verify message ordering
> - Check duplicate prevention
> ```

### Load Testing

Test with multiple users:

```bash
# Simple load test
for i in {1..10}; do
  curl -X POST http://localhost:3001/simulate-user &
done
```

## Part 8: Optimize WebSocket Performance

Fine-tune for low-bandwidth:

> **🤖 Continue Prompt:**
> ```
> Optimize WebSocket for low bandwidth:
> - Enable per-message compression
> - Batch messages (100ms window)
> - Binary protocol for smaller payload
> - Delta compression for updates
> - Minimize heartbeat frequency
> - Implement message priorities
> ```

## Part 9: Add Admin Features

Implement admin controls over WebSocket:

> **🤖 Continue Prompt:**
> ```
> Add admin WebSocket commands:
> - /clear - Clear all messages
> - /kick [user] - Disconnect user
> - /broadcast [msg] - System message
> - /stats - Server statistics
> - /mute [user] - Prevent user messages
> - Rate limit overrides for admin
> ```

## Part 10: Commit Your Work

```bash
git add .
git commit -m "feat: implement Phase 2 - WebSocket real-time communication

- WebSocket server with compression
- Auto-reconnect client
- User presence tracking
- Message synchronization
- Connection status UI
"
```

## ✅ Checkpoint

Verify real-time features:

1. **Multi-User Test:**
   - [ ] Open 3+ browser windows
   - [ ] Different users logged in
   - [ ] Messages appear instantly
   - [ ] User count updates

2. **Resilience Test:**
   - [ ] Kill WebSocket server
   - [ ] Messages queue offline
   - [ ] Restart server
   - [ ] Auto-reconnects
   - [ ] Queued messages send

3. **Performance Check:**
   ```bash
   npm run build
   ```
   - [ ] Bundle still < 50KB
   - [ ] WebSocket adds < 10KB

## Common Issues

<details>
<summary>WebSocket connection refused?</summary>

Check server is running:
```bash
# Check if port 3001 is in use
lsof -i :3001

# Make sure both dev servers are running
npm run dev:all
```

</details>

<details>
<summary>Messages not syncing?</summary>

Debug WebSocket in console:
```javascript
// Check connection
console.log(websocket.readyState);
// 0 = CONNECTING, 1 = OPEN, 2 = CLOSING, 3 = CLOSED

// Monitor WebSocket frames
// DevTools → Network → WS → Messages
```

</details>

<details>
<summary>High latency/slow messages?</summary>

Check compression:
```javascript
// Log message sizes
console.log('Original:', text.length);
console.log('Compressed:', LZString.compress(text).length);
```

</details>

## Performance Metrics

### Target Benchmarks
- **Connection Time:** < 500ms
- **Message Latency:** < 100ms
- **Reconnect Time:** < 2 seconds
- **Memory per Connection:** < 1MB

### Monitoring

> **🤖 Continue Prompt:**
> ```
> Add WebSocket metrics:
> - Connection duration tracking
> - Message throughput monitoring
> - Reconnection frequency
> - Compression ratios
> - Error rates
> ```

## What You've Accomplished

🚀 Fantastic work! Your chat now has:
- 🔄 Real-time message delivery
- 👥 Live user presence
- 🔌 Automatic reconnection
- 📊 Connection status feedback
- 🔐 Admin controls
- 📦 Optimized for low-bandwidth

Your app now rivals commercial chat applications! Next, we'll optimize for production.

**[→ Continue to Step 5: Production Optimization](./step-5-optimization.md)**

---

## Advanced Features

### Extra Challenges

1. **Voice Messages:**
   > Add compressed audio messages using WebRTC

2. **File Sharing:**
   > Implement chunked file transfer over WebSocket

3. **End-to-End Encryption:**
   > Add E2E encryption using Web Crypto API

## Debugging Tools

```javascript
// WebSocket debugging helper
window.wsDebug = {
  log: () => console.log(websocket),
  send: (msg) => websocket.send(msg),
  close: () => websocket.close(),
  reconnect: () => websocket.reconnect()
};
```

---

**[← Back to Step 3](./step-3-pwa-offline.md)** | **[→ Next: Optimization](./step-5-optimization.md)**