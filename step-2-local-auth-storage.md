# Step 2: Local Authentication & Storage 🔐

Build the core chat functionality with browser-based authentication and persistent storage using IndexedDB.

**Time:** ~30 minutes  
**Outcome:** Working chat UI with local auth and message storage

## Prerequisites

- [ ] Completed [Step 1: Project Setup](./step-1-project-setup.md)
- [ ] Dev server runs successfully
- [ ] TypeScript configured

## What You'll Build

In this step:
- ✅ Browser-based authentication system
- ✅ IndexedDB storage with compression
- ✅ Mobile-first chat UI
- ✅ Message history management
- ✅ Admin controls

## Part 1: Create the Authentication Module

Let's build a simple, secure authentication system that works entirely in the browser:

> **🤖 Continue Prompt:**
> ```
> Create src/auth.ts with a browser-based authentication system:
> - Username-only authentication (no password)
> - Generate local JWT using Web Crypto API
> - Store auth state in localStorage
> - Methods: login(username), logout(), getUser(), isAuthenticated()
> - Generate unique user ID with crypto.randomUUID()
> - Add admin check based on URL parameter
> ```

Expected structure:

```typescript
// src/auth.ts
interface User {
  id: string;
  username: string;
  isAdmin: boolean;
  token: string;
}

class AuthManager {
  private currentUser: User | null = null;
  
  async login(username: string): Promise<User> { /* ... */ }
  logout(): void { /* ... */ }
  getUser(): User | null { /* ... */ }
  isAuthenticated(): boolean { /* ... */ }
  isAdmin(): boolean { /* ... */ }
}
```

## Part 2: Implement IndexedDB Storage

Create a robust storage layer with compression:

> **🤖 Continue Prompt:**
> ```
> Create src/storage.ts using the idb library:
> - Database name: 'homeroom-chat'
> - Store: 'messages' with indexes on timestamp and userId
> - Message interface with: id, userId, username, text, timestamp, compressed
> - Use lz-string to compress/decompress message text
> - Methods: saveMessage(), getMessages(limit), clearMessages()
> - Implement message cleanup (keep last 100 messages)
> - Add retry logic for failed operations
> ```

Key features to implement:
- Automatic compression for messages > 100 characters
- Pagination support for large message histories
- Error recovery with exponential backoff

## Part 3: Build the Chat UI

Create the main chat interface:

> **🤖 Continue Prompt:**
> ```
> Create src/chat.ts with these components:
> - Mobile-first chat UI with virtual scrolling
> - Message list container with auto-scroll
> - Input field with send button
> - Status indicator (online/offline)
> - renderMessage() function with HTML sanitization
> - Support for system messages
> - Admin controls panel (hidden by default)
> - Responsive design for mobile/desktop
> ```

The UI should follow this structure:

```typescript
class ChatUI {
  private container: HTMLElement;
  private messageList: HTMLElement;
  private inputField: HTMLInputElement;
  
  init(): void { /* ... */ }
  addMessage(message: Message): void { /* ... */ }
  clearMessages(): void { /* ... */ }
  showAdminControls(): void { /* ... */ }
}
```

## Part 4: Create the Styles

Design a clean, mobile-optimized interface:

> **🤖 Continue Prompt:**
> ```
> Create src/styles.css with:
> - Mobile-first responsive design
> - System font stack for performance
> - Dark/light theme CSS variables
> - Chat bubble styles (sent/received)
> - Fixed input bar at bottom
> - Virtual scrolling container
> - Status indicator styles
> - Max width 600px for desktop
> - Touch-friendly button sizes (min 44px)
> - Smooth animations under 200ms
> ```

Key design principles:
- Minimize repaints/reflows
- Use CSS containment for performance
- Hardware-accelerated animations only

## Part 5: Wire Everything Together

Create the main application entry point:

> **🤖 Continue Prompt:**
> ```
> Create src/main.ts that:
> - Initializes auth, storage, and chat modules
> - Handles login flow if not authenticated
> - Loads message history from IndexedDB
> - Sets up event listeners for sending messages
> - Implements keyboard shortcuts (Enter to send)
> - Adds offline queue for messages
> - Shows admin panel if user is admin
> - Handles errors gracefully with user feedback
> ```

## Part 6: Update HTML

Enhance the HTML with chat structure:

> **🤖 Continue Prompt:**
> ```
> Update index.html with:
> - Chat container structure
> - Login modal HTML
> - Meta tags for PWA
> - Link to styles.css
> - Loading indicator
> - Noscript fallback
> ```

## Part 7: Test Your Implementation

### Manual Testing Checklist

1. **Authentication Flow:**
   ```bash
   npm run dev
   ```
   - [ ] Can enter username
   - [ ] Username persists on refresh
   - [ ] Logout works correctly

2. **Message Storage:**
   - [ ] Send a message
   - [ ] Refresh page - message persists
   - [ ] Messages are compressed (check DevTools)

3. **Admin Features:**
   - [ ] Add `?admin=true` to URL
   - [ ] Admin controls appear
   - [ ] Can clear all messages

4. **Mobile Testing:**
   - [ ] Open DevTools → Device Mode
   - [ ] Test on iPhone SE size
   - [ ] Input doesn't cause zoom
   - [ ] Scrolling is smooth

### Performance Check

> **🤖 Continue Prompt:**
> ```
> Add performance monitoring to main.ts:
> - Log storage operations timing
> - Measure message render time
> - Track memory usage
> - Alert if operations > 100ms
> ```

## Part 8: Commit Your Progress

```bash
git add .
git commit -m "feat: implement Phase 1 - local auth and storage

- Browser-based authentication system
- IndexedDB storage with compression
- Mobile-first chat UI
- Message history management
- Admin controls
"
```

## ✅ Checkpoint

Verify your implementation:

1. **Build Check:**
   ```bash
   npm run build
   ```
   Check the dist folder size (should be < 50KB)

2. **TypeScript Check:**
   ```bash
   npx tsc --noEmit
   ```

3. **Feature Verification:**
   - [ ] Login with username works
   - [ ] Messages save to IndexedDB
   - [ ] Messages compress properly
   - [ ] UI is responsive on mobile
   - [ ] Admin panel accessible with URL param

## Common Issues

<details>
<summary>IndexedDB not working?</summary>

Check browser support:
```javascript
if (!('indexedDB' in window)) {
  console.error('IndexedDB not supported');
}
```

Try incognito mode to rule out extensions.

</details>

<details>
<summary>Messages not persisting?</summary>

Open DevTools → Application → IndexedDB and verify:
- Database exists
- Messages are stored
- No quota errors

</details>

<details>
<summary>Compression not working?</summary>

Test lz-string directly:
```javascript
import LZString from 'lz-string';
const compressed = LZString.compress('test message');
console.log('Compressed size:', compressed.length);
```

</details>

## Optimization Tips

### Bundle Size
- Check with: `npx vite-bundle-visualizer`
- Remove unused imports
- Use dynamic imports for large features

### Performance
- Implement virtual scrolling for > 50 messages
- Debounce input events
- Use `requestAnimationFrame` for UI updates

## What You've Accomplished

🎉 Congratulations! You now have:
- 🔐 Secure browser-based authentication
- 💾 Persistent message storage with compression
- 📱 Mobile-optimized chat interface
- 👤 Admin controls for moderation
- ⚡ Performance optimizations

Your app now works completely offline! In the next step, we'll make it a true PWA with service workers.

**[→ Continue to Step 3: PWA & Offline Mode](./step-3-pwa-offline.md)**

---

## Code Reference

Key files created:
- `src/auth.ts` - Authentication system
- `src/storage.ts` - IndexedDB operations
- `src/chat.ts` - UI components
- `src/styles.css` - Mobile-first styles
- `src/main.ts` - Application entry

## Extra Challenges

Want to go further? Try these enhancements:

1. **Add Emoji Support:**
   > Use Continue to add an emoji picker using Unicode emojis only

2. **Message Timestamps:**
   > Add relative timestamps (e.g., "2 min ago")

3. **Typing Indicators:**
   > Show when someone is typing (local only for now)

---

**[← Back to Step 1](./step-1-project-setup.md)** | **[→ Next: PWA & Offline](./step-3-pwa-offline.md)**