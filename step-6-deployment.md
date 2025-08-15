# Step 6: Deploy to Production 🌍

Deploy your local-first chat app to production with multiple deployment options and monitoring.

**Time:** ~15 minutes  
**Outcome:** Live production app accessible from anywhere

## Prerequisites

- [ ] Completed [Step 5: Production Optimization](./step-5-optimization.md)
- [ ] Bundle optimized < 50KB
- [ ] Production build passing
- [ ] All tests passing

## What You'll Deploy

In this step:
- ✅ Deploy to Vercel/Netlify/Railway
- ✅ Configure custom domain
- ✅ Set up monitoring
- ✅ Enable analytics
- ✅ Configure auto-deployment
- ✅ Add uptime monitoring

## Deployment Options

Choose your preferred platform:

### Option A: Vercel (Recommended)

> 💡 **Why Vercel?** Edge network with 100+ PoPs globally. Automatic HTTPS, instant cache invalidation, and serverless functions. Free tier handles 100GB bandwidth. [Vercel architecture →](https://vercel.com/docs/concepts/how-vercel-works)

#### Part 1: Deploy Frontend

> **🤖 Continue Prompt:**
> ```
> Set up Vercel deployment:
> 1. Install Vercel CLI: npm i -g vercel
> 2. Create vercel.json with:
>    - Build settings
>    - Environment variables
>    - Headers (CSP, CORS)
>    - Redirects
> 3. Deploy with: vercel --prod
> ```

Create `vercel.json`:
```json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "framework": "vite",
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        },
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        },
        {
          "key": "X-XSS-Protection",
          "value": "1; mode=block"
        }
      ]
    }
  ]
}
```

Deploy command:
```bash
# Deploy to Vercel
vercel --prod

# Set environment variables
vercel env add VITE_WS_URL production
```

#### Part 2: Deploy WebSocket Server

> 💡 **WebSocket Hosting:** Railway supports persistent connections, unlike serverless platforms. Auto-scales based on CPU/memory. $5/month handles ~1000 concurrent users. [Railway WebSocket guide →](https://docs.railway.app/deploy/deployments#websocket-support)

> **🤖 Continue Prompt:**
> ```
> Deploy WebSocket server to Railway:
> 1. Create railway.json configuration
> 2. Set up WebSocket port binding
> 3. Configure health checks
> 4. Enable auto-scaling
> 5. Set environment variables
> ```

### Option B: Netlify

> 💡 **Netlify Features:** Built-in forms, serverless functions, split testing. Edge handlers for A/B tests. 100GB bandwidth free tier. [Netlify vs Vercel comparison →](https://www.netlify.com/blog/netlify-vs-vercel-a-comparison/)

#### Setup Netlify Deployment

> **🤖 Continue Prompt:**
> ```
> Configure Netlify deployment:
> 1. Create netlify.toml with:
>    - Build command: npm run build
>    - Publish directory: dist
>    - Environment variables
>    - Redirect rules
>    - Headers configuration
> 2. Deploy with Netlify CLI
> ```

Create `netlify.toml`:
```toml
[build]
  command = "npm run build"
  publish = "dist"

[build.environment]
  NODE_VERSION = "18"

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
    X-Content-Type-Options = "nosniff"
    Content-Security-Policy = "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; connect-src 'self' wss://*"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

Deploy:
```bash
# Install Netlify CLI
npm i -g netlify-cli

# Deploy
netlify deploy --prod
```

### Option C: Self-Hosted

> 💡 **Self-Hosting Benefits:** Full control, no vendor lock-in, unlimited bandwidth. Costs ~$5/month on DigitalOcean. Requires DevOps knowledge. [Docker production guide →](https://docs.docker.com/get-started/09_image_best/)

#### Docker Deployment

> **🤖 Continue Prompt:**
> ```
> Create Docker deployment:
> 1. Create Dockerfile for frontend
> 2. Create docker-compose.yml for full stack
> 3. Add nginx.conf for serving
> 4. Configure SSL with Let's Encrypt
> 5. Set up GitHub Actions for CI/CD
> ```

Create `Dockerfile`:
```dockerfile
# Build stage
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## Part 3: Domain Configuration

> 💡 **DNS Propagation:** Takes 0-48 hours globally. Use DNS checker tools. Cloudflare DNS is fastest (1.1.1.1). [DNS propagation checker →](https://www.whatsmydns.net/)

Configure custom domain:

> **🤖 Continue Prompt:**
> ```
> Set up custom domain:
> 1. Add domain to deployment platform
> 2. Configure DNS records:
>    - A record for apex domain
>    - CNAME for www subdomain
> 3. Enable SSL/TLS
> 4. Configure HSTS
> 5. Set up subdomain for WebSocket (ws.yourdomain.com)
> ```

DNS Configuration:
```
Type    Name    Value
A       @       76.76.21.21
CNAME   www     your-app.vercel.app
CNAME   ws      your-ws-server.railway.app
```

## Part 4: Monitoring Setup

> 💡 **Monitoring Stack:** Sentry captures 95% of JS errors. 0.1% sampling keeps costs low. Plausible is GDPR-compliant, no cookie banner needed. [Error monitoring best practices →](https://docs.sentry.io/platforms/javascript/best-practices/)

Add production monitoring:

> **🤖 Continue Prompt:**
> ```
> Set up monitoring with:
> 1. Sentry for error tracking:
>    - Install @sentry/vite-plugin
>    - Configure source maps
>    - Set up error boundaries
>    - Add performance monitoring
> 
> 2. Analytics (Privacy-friendly):
>    - Plausible or Umami
>    - Custom events for key actions
>    - No cookies required
> ```

Sentry configuration:
```typescript
// src/monitoring.ts
import * as Sentry from "@sentry/browser";

Sentry.init({
  dsn: import.meta.env.VITE_SENTRY_DSN,
  environment: import.meta.env.MODE,
  tracesSampleRate: 0.1,
  integrations: [
    new Sentry.BrowserTracing(),
  ],
});
```

## Part 5: CI/CD Pipeline

Automate deployment:

> **🤖 Continue Prompt:**
> ```
> Create .github/workflows/deploy.yml:
> - Trigger on push to main
> - Run tests
> - Check bundle size
> - Run Lighthouse CI
> - Deploy if all checks pass
> - Send notification on success/failure
> ```

GitHub Actions workflow:
```yaml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      
      - run: npm ci
      - run: npm test
      - run: npm run build
      
      - name: Check bundle size
        run: |
          size=$(du -sb dist | cut -f1)
          if [ $size -gt 51200 ]; then
            echo "Bundle too large: $size bytes"
            exit 1
          fi
      
      - name: Deploy to Vercel
        run: vercel --prod --token=${{ secrets.VERCEL_TOKEN }}
```

## Part 6: Performance Monitoring

Set up real user monitoring:

> **🤖 Continue Prompt:**
> ```
> Add RUM (Real User Monitoring):
> 1. Web Vitals reporting
> 2. Custom performance marks
> 3. Resource timing
> 4. Long task detection
> 5. Dashboard for metrics
> ```

## Part 7: Backup & Recovery

Implement backup strategy:

> **🤖 Continue Prompt:**
> ```
> Create backup system:
> 1. Automated daily backups
> 2. Point-in-time recovery
> 3. Geo-redundant storage
> 4. Backup verification
> 5. Recovery procedures document
> ```

## Part 8: Security Headers

Configure security headers:

```javascript
// vercel.json or netlify.toml headers
{
  "headers": [
    {
      "key": "Content-Security-Policy",
      "value": "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; connect-src 'self' wss://*; img-src 'self' data: blob:; font-src 'self';"
    },
    {
      "key": "Strict-Transport-Security",
      "value": "max-age=31536000; includeSubDomains; preload"
    },
    {
      "key": "Permissions-Policy",
      "value": "camera=(), microphone=(), geolocation=()"
    }
  ]
}
```

## Part 9: Load Testing

> 💡 **Load Testing Goals:** Find breaking point before users do. 1000 concurrent = small chat app. WebSocket uses 50KB RAM per connection. [k6 WebSocket testing →](https://k6.io/docs/using-k6/protocols/websockets/)

Test production capacity:

> **🤖 Continue Prompt:**
> ```
> Run load tests with:
> 1. Set up k6 or Artillery
> 2. Test WebSocket connections
> 3. Simulate 1000 concurrent users
> 4. Monitor server resources
> 5. Document maximum capacity
> ```

Load test script:
```javascript
// load-test.js
import { check } from 'k6';
import ws from 'k6/ws';

export default function() {
  const url = 'wss://ws.yourdomain.com';
  const res = ws.connect(url, {}, function(socket) {
    socket.on('open', () => {
      socket.send(JSON.stringify({
        type: 'message',
        text: 'Load test message'
      }));
    });
  });
  
  check(res, { 'status is 101': (r) => r && r.status === 101 });
}
```

## Part 10: Rollback Plan

Prepare rollback strategy:

> **🤖 Continue Prompt:**
> ```
> Create rollback procedure:
> 1. Keep 3 previous versions
> 2. One-click rollback
> 3. Database migration rollback
> 4. Feature flags for gradual rollout
> 5. Rollback checklist document
> ```

## ✅ Deployment Checklist

Verify your deployment:

### Pre-Deployment
- [ ] All tests passing
- [ ] Bundle size < 50KB
- [ ] No console.logs in production
- [ ] Environment variables set
- [ ] SSL certificate active

### Post-Deployment
- [ ] Site accessible via HTTPS
- [ ] WebSocket connection working
- [ ] PWA installable
- [ ] Lighthouse score > 95
- [ ] Monitoring active
- [ ] Backup system working

### Security
- [ ] CSP headers configured
- [ ] CORS properly set
- [ ] Rate limiting active
- [ ] Input validation working
- [ ] XSS protection enabled

## Common Deployment Issues

<details>
<summary>WebSocket connection failing?</summary>

Check:
1. WSS protocol in production
2. CORS headers allow origin
3. Firewall allows WebSocket
4. Proxy configuration

```javascript
// Ensure WSS in production
const wsUrl = location.protocol === 'https:' 
  ? 'wss://ws.yourdomain.com' 
  : 'ws://localhost:3001';
```

</details>

<details>
<summary>Build failing on deploy?</summary>

Common fixes:
```bash
# Clear cache
rm -rf node_modules package-lock.json
npm install

# Check Node version
node --version  # Should match deployment platform

# Verify build locally
npm run build
```

</details>

<details>
<summary>PWA not installing?</summary>

Requirements:
- HTTPS enabled
- Valid manifest.json
- Service worker registered
- Icons provided
- Start URL accessible

</details>

## Monitoring Dashboard

### Key Metrics to Track
| Metric | Target | Alert Threshold |
|--------|--------|-----------------|
| Uptime | 99.9% | < 99% |
| Response Time | < 200ms | > 500ms |
| Error Rate | < 0.1% | > 1% |
| Active Users | - | Sudden drop |
| WebSocket Connections | - | > 80% capacity |

> 💡 **SLA Math:** 99.9% uptime = 43 minutes downtime/month. 99.99% = 4 minutes. Each 9 costs 10x more. [Uptime calculator →](https://uptime.is/)

## Post-Deployment Tasks

1. **Share with users:**
   ```
   🎉 Your app is live at: https://yourdomain.com
   ```

2. **Monitor first 24 hours:**
   - Check error logs
   - Monitor performance
   - Gather user feedback

3. **Document everything:**
   - Deployment process
   - Environment variables
   - Troubleshooting guide

## What You've Accomplished

🚀 **Congratulations!** Your local-first chat app is now live with:
- 🌍 Global accessibility
- 📊 Production monitoring
- 🔒 Security hardened
- ⚡ Optimized performance
- 🔄 Automated deployment
- 📱 PWA ready

Your chat app is production-ready and accessible worldwide!

## Next Steps

### Enhance Your App

1. **Add Features:**
   - Video/Audio calls (WebRTC, adds ~100KB)
   - File sharing (chunked upload, S3 storage)
   - Reactions/Emojis (Unicode, 0KB extra)
   - Read receipts (WebSocket events)
   - Typing indicators (debounced, 1-2s delay)

2. **Scale Up:**
   - Redis for sessions (handles 100K ops/sec)
   - PostgreSQL for persistence (JSONB for messages)
   - CDN for assets (CloudFlare free tier)
   - Multiple server regions (< 100ms latency globally)

3. **Monetization:**
   - Premium features ($5-10/month typical)
   - Team workspaces (Slack model)
   - Storage limits (10GB free, then pay)
   - Custom themes (one-time purchase)

> 💡 **Scaling Path:** Start with single server → add Redis → add DB → multi-region. Each step 10x capacity. [Scaling WebSocket apps →](https://ably.com/topic/scaling-websocket-connections)

## Resources

- [Web.dev Performance](https://web.dev/performance/)
- [Vercel Documentation](https://vercel.com/docs)
- [Netlify Documentation](https://docs.netlify.com)
- [Railway Documentation](https://docs.railway.app)

---

**[← Back to Step 5](./step-5-optimization.md)** | **[→ Back to Overview](./README.md)**

## 🎉 Congratulations!

You've successfully built and deployed a production-ready local-first chat application! 

Your app features:
- 💾 Local-first architecture
- 🔐 Secure authentication
- 📱 PWA with offline support
- ⚡ Real-time messaging
- 📦 < 50KB optimized bundle
- 🌍 Global deployment

Share your success: Tag us with #LocalFirstChat

---

### Continue Learning

- [Local-First Software](https://www.inkandswitch.com/local-first/)
- [CRDTs Explained](https://crdt.tech/)
- [PWA Best Practices](https://web.dev/progressive-web-apps/)