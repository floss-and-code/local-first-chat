# Step 3: PWA & Offline Mode 📱

Transform your chat app into a Progressive Web App with full offline functionality, installability, and background sync.

**Time:** ~25 minutes  
**Outcome:** Installable PWA that works completely offline

## Prerequisites

- [ ] Completed [Step 2: Local Auth & Storage](./step-2-local-auth-storage.md)
- [ ] Chat UI working with local storage
- [ ] Messages persisting in IndexedDB

## What You'll Build

In this step:
- ✅ Service Worker for offline caching
- ✅ PWA manifest for installability
- ✅ Offline message queue
- ✅ Background sync for queued messages
- ✅ App update notifications

## Part 1: Configure PWA Plugin

> 💡 **PWA Benefits:** Native app-like experience with 3x higher engagement. Service workers enable offline functionality, background sync, and push notifications. [PWA statistics →](https://web.dev/articles/what-are-pwas)

Update your Vite configuration for PWA support:

> **🤖 Continue Prompt:**
> ```
> Update vite.config.ts to configure vite-plugin-pwa:
> - Auto-generate service worker with Workbox
> - Cache all assets for offline use
> - Runtime caching for API calls
> - Skip waiting for immediate updates
> - Manifest with app icons and theme
> - Offline fallback page
> - Background sync registration
> ```

Expected configuration:

```typescript
// vite.config.ts
import { VitePWA } from 'vite-plugin-pwa';

export default {
  plugins: [
    VitePWA({
      registerType: 'autoUpdate',
      includeAssets: ['icon.svg'],
      manifest: { /* ... */ },
      workbox: { /* ... */ }
    })
  ]
}
```

## Part 2: Create PWA Manifest

Design your app's identity:

> **🤖 Continue Prompt:**
> ```
> Create public/manifest.json with:
> - Name: "Homeroom Chat"
> - Short name: "Homeroom"
> - Description for low-bandwidth chat
> - Start URL with standalone display
> - Theme color matching your design
> - Background color for splash screen
> - Icon sizes: 192x192, 512x512
> - Orientation: portrait preferred
> - Categories: ["social", "productivity"]
> ```

Create a simple SVG icon:

> **🤖 Continue Prompt:**
> ```
> Create public/icon.svg:
> - Simple chat bubble design
> - Scalable vector graphics
> - Works at all sizes
> - Single color for simplicity
> ```

## Part 3: Implement Service Worker

> 💡 **Caching Strategies:**
> - **Cache-First:** Static assets (CSS, JS) - serves instantly from cache
> - **Network-First:** Dynamic content - ensures freshness
> - **Stale-While-Revalidate:** Balance between speed and freshness
> [Caching strategies deep dive →](https://web.dev/articles/offline-cookbook)

Create advanced caching strategies:

> **🤖 Continue Prompt:**
> ```
> Create src/service-worker.ts with:
> - Cache-first strategy for assets
> - Network-first for API calls
> - Stale-while-revalidate for images
> - Offline fallback handling
> - Cache versioning system
> - Background sync for failed messages
> - Push notification support (optional)
> - Skip waiting on update
> ```

Key features:
```typescript
// Caching strategies
const cacheFirst = async (request: Request) => { /* ... */ };
const networkFirst = async (request: Request) => { /* ... */ };

// Background sync
self.addEventListener('sync', (event) => {
  if (event.tag === 'send-messages') {
    event.waitUntil(sendQueuedMessages());
  }
});
```

> 💡 **Background Sync:** Guarantees message delivery even if app is closed. Retries automatically when connection returns. [Background Sync API →](https://developer.chrome.com/docs/capabilities/periodic-background-sync)
```

## Part 4: Create Offline Queue

> 💡 **Offline-First Architecture:** Queue + sync ensures zero message loss. IndexedDB persists queue across sessions. Exponential backoff prevents server overload (2s, 4s, 8s...). [Offline patterns →](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook)

Implement message queueing for offline scenarios:

> **🤖 Continue Prompt:**
> ```
> Create src/offline-queue.ts:
> - Queue messages when offline
> - Store queue in IndexedDB
> - Retry with exponential backoff
> - Register background sync
> - Methods: enqueue(), processQueue(), clear()
> - Status callbacks for UI updates
> - Maximum retry attempts: 3
> ```

Queue structure:
```typescript
interface QueuedMessage {
  id: string;
  message: Message;
  attempts: number;
  timestamp: number;
}

class OfflineQueue {
  async enqueue(message: Message): Promise<void> { /* ... */ }
  async processQueue(): Promise<void> { /* ... */ }
  async registerSync(): Promise<void> { /* ... */ }
}
```

## Part 5: Add Update Notifications

> 💡 **Update Strategy:** `autoUpdate` ensures users always have latest version. Skip-waiting pattern minimizes downtime. Critical for fixing bugs in production. [Service Worker lifecycle →](https://web.dev/articles/service-worker-lifecycle)

Notify users when app updates are available:

> **🤖 Continue Prompt:**
> ```
> Update src/main.ts to handle PWA lifecycle:
> - Register service worker
> - Listen for updates
> - Show update prompt to user
> - Implement app refresh
> - Handle offline/online events
> - Update UI connection status
> - Cache user preferences
> ```

Add update UI:
```typescript
// Show update banner
function showUpdatePrompt() {
  const banner = document.createElement('div');
  banner.className = 'update-banner';
  banner.innerHTML = `
    <span>New version available!</span>
    <button onclick="window.location.reload()">Update</button>
  `;
}
```

## Part 6: Enhance Offline UI

Improve the offline experience:

> **🤖 Continue Prompt:**
> ```
> Update src/chat.ts to handle offline mode:
> - Show offline indicator
> - Display queue status
> - Gray out sent button when offline
> - Show pending messages differently
> - Add retry button for failed messages
> - Display "Sending..." state
> - Cache user avatar/preferences
> ```

Visual indicators:
```css
/* Offline styles */
.offline .message-input { opacity: 0.7; }
.message.pending { opacity: 0.6; }
.message.failed { border-left: 3px solid red; }
```

## Part 7: Test PWA Features

### Installation Test

1. **Build and serve production:**
   ```bash
   npm run build
   npm run preview
   ```

2. **Test installation:**
   - Open in Chrome/Edge
   - Look for install prompt in address bar
   - Install and launch as standalone app

3. **Test on mobile:**
   - Open on phone browser
   - "Add to Home Screen" option
   - Launch from home screen

### Offline Test

1. **Go offline:**
   - DevTools → Network → Offline
   - Or airplane mode on mobile

2. **Verify functionality:**
   - [ ] App still loads
   - [ ] Can send messages (queued)
   - [ ] UI shows offline status
   - [ ] Previous messages visible

3. **Go back online:**
   - [ ] Queued messages send
   - [ ] Status updates to online

### Update Test

> **🤖 Continue Prompt:**
> ```
> Add version number to main.ts and test updates:
> - Display version in UI
> - Change version and rebuild
> - Test update notification appears
> - Verify skip waiting works
> ```

## Part 8: Optimize Service Worker

> 💡 **Cache Management:** Limiting cache size prevents storage bloat. 7-day expiration balances freshness with performance. Cache warming pre-loads critical paths. [Cache storage best practices →](https://web.dev/articles/cache-api-quick-guide)

Fine-tune caching for performance:

> **🤖 Continue Prompt:**
> ```
> Optimize service worker caching:
> - Pre-cache critical assets
> - Limit cache sizes (max 50 entries)
> - Set expiration for caches (7 days)
> - Clean old caches on activate
> - Add telemetry for cache hits/misses
> - Implement cache warming strategy
> ```

## Part 9: Commit Your PWA

```bash
git add .
git commit -m "feat: implement PWA with offline support

- Service worker with smart caching
- Installable PWA manifest
- Offline message queue
- Background sync for messages
- Update notifications
"
```

## ✅ Checkpoint

Verify your PWA implementation:

1. **Lighthouse Audit:**
   ```bash
   npm run build
   npm run preview
   ```
   - Open Chrome DevTools → Lighthouse
   - Run PWA audit
   - Score should be > 90

2. **Installation:**
   - [ ] App installs on desktop
   - [ ] App installs on mobile
   - [ ] Launches in standalone mode
   - [ ] Icon appears correctly

3. **Offline Features:**
   - [ ] Works completely offline
   - [ ] Messages queue when offline
   - [ ] Sync when back online
   - [ ] UI indicates connection status

## Common Issues

<details>
<summary>Service Worker not registering?</summary>

Check HTTPS requirement:
- Service workers only work on HTTPS or localhost
- Use `npm run preview` for local HTTPS
- Check console for registration errors

</details>

<details>
<summary>App not installable?</summary>

Requirements checklist:
- [ ] Valid manifest.json
- [ ] Service worker registered
- [ ] HTTPS connection
- [ ] Icons provided
- [ ] Start URL accessible

</details>

<details>
<summary>Background sync not working?</summary>

Verify browser support:
```javascript
if ('sync' in registration) {
  console.log('Background sync supported');
}
```

Note: Not all browsers support background sync.

</details>

## Performance Metrics

### Target Metrics
- **Time to Install:** < 5 seconds (network dependent)
- **Offline Load Time:** < 1 second (from cache)
- **Cache Size:** < 5MB (respects quota)
- **Update Check:** Every 60 seconds (configurable)

> 💡 **Why These Targets?** 1-second load maintains user engagement. 5MB cache works on low-end devices. 60-second checks balance freshness with battery life. [Performance budgets guide →](https://web.dev/articles/performance-budgets-101)

### Monitoring

> **🤖 Continue Prompt:**
> ```
> Add PWA metrics tracking:
> - Log service worker events
> - Track cache performance
> - Monitor sync success rate
> - Report install conversions
> ```

## What You've Accomplished

🎉 Amazing progress! Your app now has:
- 📱 Full PWA capabilities
- 🔌 Complete offline functionality
- 💾 Smart caching strategies
- 🔄 Background sync for messages
- 📲 Installable on all devices
- 🔔 Update notifications

Your chat app now rivals native apps in functionality! Next, we'll add real-time communication.

**[→ Continue to Step 4: WebSocket Real-time Communication](./step-4-websocket-realtime.md)**

---

## Advanced Features

### Extra Challenges

1. **Push Notifications:**
   > Add push notification support for new messages
   > 💡 **Requires:** VAPID keys, notification permission, push server. [Web Push protocol →](https://web.dev/articles/push-notifications-overview)

2. **Share Target:**
   > Register as a share target to receive shared text
   > 💡 **Web Share Target API:** Allows PWA to receive shared content like native apps. [Share Target API →](https://web.dev/articles/web-share-target)

3. **Shortcuts:**
   > Add app shortcuts for quick actions
   > 💡 **App Shortcuts:** Long-press app icon for quick actions. Defined in manifest. [Shortcuts guide →](https://web.dev/articles/app-shortcuts)

## Resources

- [PWA Checklist](https://web.dev/pwa-checklist/)
- [Workbox Documentation](https://developers.google.com/web/tools/workbox)
- [Service Worker Cookbook](https://serviceworke.rs/)

---

**[← Back to Step 2](./step-2-local-auth-storage.md)** | **[→ Next: WebSocket Real-time](./step-4-websocket-realtime.md)**