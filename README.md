# Build Your Own Local-First Chat with AI Coding Agents 🚀

Welcome to this hands-on workshop where you'll build a production-ready chat application optimized for low-bandwidth environments! Using AI coding agents (Continue), you'll create a Progressive Web App that works offline, syncs in real-time, and performs brilliantly on mobile devices.

## What You'll Build

A lightweight, fast chat application that:
- ⚡ Loads in < 3 seconds on 3G networks
- 📱 Works fully offline with local storage
- 🔄 Syncs messages in real-time when online
- 📦 Maintains < 50KB bundle size
- 🔒 Requires no backend authentication
- 🎯 Optimized for mobile-first experience

## Prerequisites

Before starting, ensure you have:

- [ ] **Node.js** (v18 or higher) - [Download](https://nodejs.org/)
- [ ] **Git** - [Download](https://git-scm.com/)
- [ ] **VS Code** - [Download](https://code.visualstudio.com/)
- [ ] **Continue Extension** - [Install Guide](https://continue.dev/docs/quickstart)
- [ ] Basic familiarity with TypeScript and web development

## Setting Up Continue (AI Coding Agent)

Continue is an open-source AI coding assistant that will help you build this project. Here's how to set it up:

### Step 1: Install Continue Extension

1. Open VS Code
2. Go to Extensions (Cmd/Ctrl + Shift + X)
3. Search for "Continue"
4. Click Install on the Continue extension by Continue Dev

### Step 2: Configure Continue

1. Open Continue panel (Cmd/Ctrl + Shift + L)
2. Click on the gear icon (⚙️) for settings
3. Choose your preferred AI model:
   - **Recommended**: Claude 3.5 Sonnet (requires API key)
   - **Alternative**: Local models via Ollama

### Step 3: Test Your Setup

Try this simple prompt in Continue:
```
Create a hello world TypeScript function
```

If Continue generates code, you're ready to go!

## Workshop Structure

This workshop is divided into progressive steps. Each builds upon the previous, but your app will be functional after each phase:

| Step | Title | What You'll Learn | Time |
|------|-------|-------------------|------|
| **[Step 0](#)** | Introduction & Setup | Project overview, Continue setup | 15 min |
| **[Step 1](./step-1-project-setup.md)** | Project Foundation | Vite, TypeScript, project structure | 20 min |
| **[Step 2](./step-2-local-auth-storage.md)** | Local Auth & Storage | Browser auth, IndexedDB, compression | 30 min |
| **[Step 3](./step-3-pwa-offline.md)** | PWA & Offline Mode | Service workers, offline queue | 25 min |
| **[Step 4](./step-4-websocket-realtime.md)** | Real-time Communication | WebSocket server, auto-reconnect | 30 min |
| **[Step 5](./step-5-optimization.md)** | Production Optimization | Bundle optimization, error handling | 20 min |
| **[Step 6](./step-6-deployment.md)** | Deployment | Deploy to Vercel/Netlify/Fly.io | 15 min |

## How to Use This Workshop

### Working with Continue

Throughout this workshop, you'll see prompt boxes like this:

> **🤖 Continue Prompt:**
> ```
> Create a TypeScript chat component with message history
> ```

Copy these prompts into Continue to generate the code. The AI will help you:
- Write boilerplate code faster
- Implement complex features
- Debug issues
- Optimize performance

### Best Practices

1. **Read each step completely** before starting
2. **Use the provided prompts** with Continue
3. **Review generated code** before accepting
4. **Test after each phase** - your app should work!
5. **Commit your changes** after each step

## Project Goals

By the end of this workshop, you'll have:

1. **Technical Skills**
   - Building offline-first PWAs
   - Implementing WebSocket communication
   - Optimizing for low-bandwidth environments
   - Using AI coding assistants effectively

2. **A Production App** with:
   - Full offline functionality
   - Real-time message sync
   - < 50KB bundle size
   - Mobile-optimized UI
   - Deployment-ready configuration

## Troubleshooting

Running into issues? Check our [Troubleshooting Guide](./TROUBLESHOOTING.md) for common problems and solutions.

## Ready to Start?

Let's begin building your local-first chat application!

**[→ Continue to Step 1: Project Setup](./step-1-project-setup.md)**

---

## Workshop Phases Overview

### Phase 1: Foundation (Steps 1-3)
Build the core chat functionality with local storage and offline capabilities. Your app will work completely offline after this phase.

### Phase 2: Real-time (Step 4)
Add WebSocket communication for real-time message synchronization when online.

### Phase 3: Production (Steps 5-6)
Optimize for production with performance improvements and deploy to cloud platforms.

## Additional Resources

- [Continue Documentation](https://continue.dev/docs)
- [Vite Guide](https://vitejs.dev/guide/)
- [PWA Documentation](https://web.dev/progressive-web-apps/)
- [WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)

## Need Help?

- 💬 Join the discussion in Issues
- 📚 Check the [Troubleshooting Guide](./TROUBLESHOOTING.md)
- 🤝 Share your progress with #LocalFirstChat

---

**Ready? Let's build something amazing together! 🎉**

**[→ Start Step 1: Project Setup](./step-1-project-setup.md)**