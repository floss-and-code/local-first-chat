# Troubleshooting Guide 🔧

Common issues and solutions for the Local-First Chat workshop.

## Quick Fixes

### 🚨 Getting Help

If you can't find your issue below or need additional help:

1. **Search existing issues:** [GitHub Issues](https://github.com/yourusername/local-first-chat/issues)
2. **Create a new issue:** [Report a Problem](https://github.com/yourusername/local-first-chat/issues/new)
3. **Include these details:**
   - Step number where you encountered the issue
   - Error message (full text)
   - Your environment (OS, Node version, browser)
   - What you tried to fix it

## Common Issues by Step

### Step 1: Project Setup Issues

<details>
<summary>❌ npm install fails</summary>

**Solution:**
```bash
# Clear npm cache
npm cache clean --force

# Delete node_modules and package-lock
rm -rf node_modules package-lock.json

# Try with different registry
npm install --registry https://registry.npmjs.org/
```

**Still failing?**
- Check Node version: `node --version` (should be v18+)
- Try using yarn: `yarn install`
</details>

<details>
<summary>❌ Vite not starting</summary>

**Common causes:**
1. Port 5173 already in use
2. Missing dependencies
3. TypeScript errors

**Solutions:**
```bash
# Kill process on port 5173
lsof -ti:5173 | xargs kill -9

# Or use different port
npm run dev -- --port 3000

# Reinstall Vite
npm uninstall vite
npm install vite@latest
```
</details>

<details>
<summary>❌ Continue extension not working</summary>

**Checklist:**
- [ ] Extension installed and enabled
- [ ] API key configured (if using cloud models)
- [ ] VS Code restarted after installation
- [ ] Check Continue output panel for errors

**Reset Continue:**
1. Cmd/Ctrl + Shift + P
2. "Continue: Reset Configuration"
3. Reconfigure your model
</details>

### Step 2: Storage & Auth Issues

<details>
<summary>❌ IndexedDB quota exceeded</summary>

**Error:** `QuotaExceededError`

**Solutions:**
```javascript
// Clear old data
async function clearOldMessages() {
  const db = await openDB('ChatDB', 1);
  const tx = db.transaction('messages', 'readwrite');
  const messages = await tx.store.getAll();
  
  // Keep only last 100 messages
  if (messages.length > 100) {
    const toDelete = messages.slice(0, -100);
    for (const msg of toDelete) {
      await tx.store.delete(msg.id);
    }
  }
}
```

**Browser storage limits:**
- Chrome: ~60% of free disk space
- Firefox: ~50% of free disk space
- Safari: ~1GB initially
</details>

<details>
<summary>❌ localStorage not persisting</summary>

**Common causes:**
- Private/Incognito mode
- Browser storage disabled
- Cross-origin issues

**Check storage:**
```javascript
// Test localStorage
try {
  localStorage.setItem('test', 'value');
  console.log('localStorage working');
  localStorage.removeItem('test');
} catch (e) {
  console.error('localStorage disabled:', e);
}
```
</details>

### Step 3: PWA & Service Worker Issues

<details>
<summary>❌ Service Worker not registering</summary>

**Requirements:**
- HTTPS or localhost
- Valid manifest.json
- Service worker file in correct location

**Debug steps:**
```javascript
// Check registration
navigator.serviceWorker.getRegistrations()
  .then(registrations => {
    console.log('Active SWs:', registrations);
  });

// Force unregister and re-register
navigator.serviceWorker.getRegistrations()
  .then(registrations => {
    registrations.forEach(r => r.unregister());
  })
  .then(() => {
    navigator.serviceWorker.register('/sw.js');
  });
```
</details>

<details>
<summary>❌ PWA not installable</summary>

**Requirements checklist:**
- [ ] HTTPS enabled
- [ ] Valid manifest.json
- [ ] `start_url` accessible
- [ ] At least one 512x512 icon
- [ ] Service worker registered
- [ ] `display: standalone` or `fullscreen`

**Test in Chrome DevTools:**
1. Application tab → Manifest
2. Check for errors/warnings
3. Lighthouse → PWA audit
</details>

<details>
<summary>❌ Offline mode not working</summary>

**Debug cache:**
```javascript
// Check what's cached
caches.keys().then(names => {
  names.forEach(name => {
    caches.open(name).then(cache => {
      cache.keys().then(keys => {
        console.log(`Cache ${name}:`, keys);
      });
    });
  });
});
```

**Clear and rebuild cache:**
```javascript
// Clear all caches
caches.keys().then(names => {
  names.forEach(name => caches.delete(name));
});
```
</details>

### Step 4: WebSocket Issues

<details>
<summary>❌ WebSocket connection failing</summary>

**Common errors:**
- `WebSocket connection failed`
- `Error during WebSocket handshake`
- `net::ERR_CONNECTION_REFUSED`

**Solutions:**
1. **Check server is running:**
   ```bash
   # Start WebSocket server
   npm run server
   ```

2. **Verify URL:**
   ```javascript
   // Development
   const ws = new WebSocket('ws://localhost:3001');
   
   // Production (must be WSS)
   const ws = new WebSocket('wss://your-domain.com');
   ```

3. **CORS/Proxy issues:**
   ```javascript
   // vite.config.ts - proxy WebSocket
   server: {
     proxy: {
       '/ws': {
         target: 'ws://localhost:3001',
         ws: true
       }
     }
   }
   ```
</details>

<details>
<summary>❌ Messages not syncing</summary>

**Debug WebSocket:**
```javascript
// Add logging
ws.onmessage = (event) => {
  console.log('Received:', event.data);
};

ws.onerror = (error) => {
  console.error('WebSocket error:', error);
};

ws.onclose = (event) => {
  console.log('Closed:', event.code, event.reason);
};
```

**Check message format:**
```javascript
// Ensure JSON format
ws.send(JSON.stringify({
  type: 'message',
  text: 'Hello',
  timestamp: Date.now()
}));
```
</details>

### Step 5: Optimization Issues

<details>
<summary>❌ Bundle too large</summary>

**Analyze bundle:**
```bash
# Install analyzer
npm install -D rollup-plugin-visualizer

# Build and analyze
npm run build
npx vite-bundle-visualizer
```

**Common fixes:**
1. **Remove unused dependencies:**
   ```bash
   # Find unused
   npx depcheck
   ```

2. **Dynamic imports:**
   ```javascript
   // Before
   import { BigLibrary } from 'big-library';
   
   // After - lazy load
   const { BigLibrary } = await import('big-library');
   ```

3. **Tree shaking:**
   ```javascript
   // package.json
   "sideEffects": false
   ```
</details>

<details>
<summary>❌ Performance issues</summary>

**Profile performance:**
1. Chrome DevTools → Performance
2. Start recording
3. Interact with app
4. Stop and analyze

**Common bottlenecks:**
- Too many re-renders
- Large DOM trees
- Unoptimized images
- Memory leaks

**Quick wins:**
```javascript
// Debounce expensive operations
const debouncedSearch = debounce(search, 300);

// Virtual scrolling for long lists
// Use intersection observer for lazy loading
```
</details>

### Step 6: Deployment Issues

<details>
<summary>❌ Build failing on Vercel/Netlify</summary>

**Common fixes:**
1. **Specify Node version:**
   ```json
   // package.json
   "engines": {
     "node": ">=18.0.0"
   }
   ```

2. **Environment variables:**
   - Check all vars are set in dashboard
   - Use correct prefix (VITE_)

3. **Build command:**
   ```json
   // vercel.json or netlify.toml
   "buildCommand": "npm run build",
   "outputDirectory": "dist"
   ```
</details>

<details>
<summary>❌ WebSocket not working in production</summary>

**Requirements:**
- WSS (not WS) for HTTPS sites
- Proper CORS headers
- WebSocket-compatible hosting

**Solutions:**
1. **Separate WebSocket server:**
   - Deploy to Railway, Fly.io, or Render
   - Use managed service (Pusher, Ably)

2. **Environment config:**
   ```javascript
   const wsUrl = import.meta.env.PROD
     ? 'wss://ws.yourdomain.com'
     : 'ws://localhost:3001';
   ```
</details>

## Browser-Specific Issues

### Safari/iOS

<details>
<summary>PWA limitations on iOS</summary>

**Known limitations:**
- No push notifications
- 50MB storage limit
- No background sync
- Service worker terminated after 30 seconds

**Workarounds:**
- Use local notifications
- Implement aggressive caching
- Prompt users to "Add to Home Screen"
</details>

### Firefox

<details>
<summary>IndexedDB in Private Mode</summary>

Firefox disables IndexedDB in private browsing.

**Detection:**
```javascript
async function checkStorage() {
  try {
    await indexedDB.open('test');
    return true;
  } catch {
    alert('Please disable private browsing');
    return false;
  }
}
```
</details>

## Performance Optimization

### Slow Initial Load

**Checklist:**
- [ ] Enable compression (gzip/brotli)
- [ ] Preload critical resources
- [ ] Lazy load non-critical features
- [ ] Optimize images (WebP, AVIF)
- [ ] Use CDN for assets

### Memory Leaks

**Common causes:**
- Event listeners not removed
- Timers not cleared
- Large arrays in closure
- WebSocket not closed

**Debug:**
```javascript
// Chrome DevTools → Memory → Heap Snapshot
// Take snapshots, perform actions, compare
```

## Still Stuck?

### Before Creating an Issue

1. **Search existing issues** - Someone might have had the same problem
2. **Check closed issues** - Solution might already exist
3. **Review the step's README** - Common issues are often documented

### Creating a Good Issue

**Title:** Clear and specific
```
Bad:  "Not working"
Good: "WebSocket connection fails with CORS error on Step 4"
```

**Body:** Include:
```markdown
## Environment
- OS: macOS 13.4
- Node: v18.17.0
- Browser: Chrome 119
- Step: 4

## Error
```
WebSocket connection to 'ws://localhost:3001' failed: 
Error during WebSocket handshake: Unexpected response code: 403
```

## What I've Tried
1. Checked server is running
2. Verified URL is correct
3. Disabled browser extensions

## Code
[Include relevant code snippets]
```

### Get Help

- **GitHub Issues:** [Create Issue](https://github.com/yourusername/local-first-chat/issues/new)
- **Discussions:** [Start Discussion](https://github.com/yourusername/local-first-chat/discussions)
- **Twitter:** Tag #LocalFirstChat

## Quick Reference

### Useful Commands

```bash
# Clean install
rm -rf node_modules package-lock.json
npm install

# Clear all caches
npm cache clean --force

# Kill process on port
lsof -ti:5173 | xargs kill -9

# Check what's using a port
lsof -i :5173

# Force rebuild
npm run build -- --force

# Debug mode
DEBUG=* npm run dev
```

### DevTools Shortcuts

| Action | Chrome | Firefox | Safari |
|--------|--------|---------|--------|
| Console | Cmd/Ctrl + Opt/Alt + J | Cmd/Ctrl + Shift + K | Cmd + Opt + C |
| Network | Cmd/Ctrl + Opt/Alt + I → Network | Cmd/Ctrl + Shift + E | Cmd + Opt + I → Network |
| Application | Cmd/Ctrl + Opt/Alt + I → Application | - | Cmd + Opt + I → Storage |

---

**Remember:** Every issue you report helps make this workshop better for everyone! Don't hesitate to [create an issue](https://github.com/yourusername/local-first-chat/issues/new) if you're stuck.