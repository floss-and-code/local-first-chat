# Step 1: Project Foundation 🏗️

In this step, you'll set up the project foundation using Vite, TypeScript, and create a Product Requirements Document (PRD) to guide your development.

**Time:** ~20 minutes  
**Outcome:** A fully configured TypeScript project with clear requirements

## Prerequisites

Make sure you've completed:
- [ ] [Step 0: Introduction & Setup](./README.md)
- [ ] Continue extension is working
- [ ] Node.js and Git are installed

## What You'll Build

By the end of this step:
- ✅ Vite + TypeScript project initialized
- ✅ Essential dependencies installed
- ✅ Product Requirements Document created
- ✅ Project structure established
- ✅ Git repository initialized

## Part 1: Initialize the Project

### Create Project Directory

First, let's create and navigate to your project directory:

```bash
mkdir homeroom-chat
cd homeroom-chat
git init
```

### Initialize with Vite

> **🤖 Continue Prompt:**
> ```
> Create a new Vite project with TypeScript for a chat application. 
> Initialize package.json with these requirements:
> - Name: homeroom-chat
> - TypeScript with strict mode
> - Minimal dependencies for a PWA chat app
> - Scripts for dev, build, and preview
> ```

The AI should generate a `package.json` similar to:

```json
{
  "name": "homeroom-chat",
  "private": true,
  "version": "0.0.1",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview"
  },
  "devDependencies": {
    "@types/node": "^20.11.5",
    "typescript": "^5.3.3",
    "vite": "^5.0.11"
  }
}
```

### Install Dependencies

Run the installation:

```bash
npm install
```

Then add the essential dependencies for our chat app:

> **🤖 Continue Prompt:**
> ```
> Add these dependencies to package.json for a low-bandwidth chat PWA:
> - lz-string for message compression
> - idb for IndexedDB wrapper
> - workbox for PWA/service worker
> - vite-plugin-pwa for PWA configuration
> Include appropriate TypeScript types
> ```

Install the new dependencies:

```bash
npm install lz-string idb
npm install -D @types/lz-string vite-plugin-pwa workbox-window
```

## Part 2: Create the Product Requirements Document

A PRD helps guide development and provides context for your AI coding assistant. Let's create one:

> **🤖 Continue Prompt:**
> ```
> Create a comprehensive PRD.md file for a low-bandwidth chat PWA with these requirements:
> 
> Target Users:
> - Mobile users in low-bandwidth areas (2G/3G networks)
> - Focus on Nigeria and similar markets
> - Need reliable chat during demos/presentations
> 
> Technical Constraints:
> - Bundle size < 50KB
> - Load time < 3 seconds on 3G
> - Works fully offline
> - Message compression
> - No backend auth required
> 
> Core Features:
> - Username-only authentication
> - Text messaging with compression
> - PWA installable
> - Offline message queue
> - Admin controls via URL parameter
> - Virtual scrolling for performance
> 
> Include sections for:
> - Executive Summary
> - User Personas
> - Technical Architecture
> - Performance Metrics
> - Implementation Phases
> - Success Criteria
> ```

This will create a detailed PRD that serves as your north star throughout development.

## Part 3: Configure TypeScript

Create a strict TypeScript configuration:

> **🤖 Continue Prompt:**
> ```
> Create tsconfig.json with:
> - Strict mode enabled
> - Target ES2020
> - Module ESNext
> - Support for Vite's module resolution
> - Include src directory
> - Proper DOM lib references
> ```

Expected `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "preserve",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "types": ["vite/client", "node"]
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

## Part 4: Set Up Project Structure

Create the basic directory structure:

```bash
mkdir src public
touch src/main.ts
touch index.html
```

> **🤖 Continue Prompt:**
> ```
> Create a basic index.html file for the chat app with:
> - Mobile viewport meta tag
> - PWA meta tags (theme color, description)
> - Minimal HTML structure
> - Script tag for src/main.ts
> - Focus on performance and mobile-first
> ```

## Part 5: Create Vite Configuration

Configure Vite for optimal PWA development:

> **🤖 Continue Prompt:**
> ```
> Create vite.config.ts with:
> - PWA plugin configuration
> - Optimal build settings for < 50KB bundle
> - Code splitting strategy
> - Compression settings
> - Service worker configuration
> - Target modern browsers only
> ```

## Part 6: Initialize Git and First Commit

Add a `.gitignore` file:

> **🤖 Continue Prompt:**
> ```
> Create a .gitignore file for a TypeScript Vite project including:
> - Node modules
> - Build outputs
> - Environment files
> - IDE configurations
> - OS files
> ```

Now commit your initial setup:

```bash
git add .
git commit -m "feat: initial project setup with Vite, TypeScript, and PRD"
```

## ✅ Checkpoint

Before proceeding, verify:

1. **Run the dev server:**
   ```bash
   npm run dev
   ```
   You should see Vite running at http://localhost:5173

2. **Check TypeScript:**
   ```bash
   npx tsc --noEmit
   ```
   Should complete without errors

3. **Review your files:**
   - [ ] `package.json` with all dependencies
   - [ ] `PRD.md` with requirements
   - [ ] `tsconfig.json` with strict mode
   - [ ] `vite.config.ts` with PWA plugin
   - [ ] `index.html` with mobile meta tags
   - [ ] `.gitignore` properly configured

## Common Issues

<details>
<summary>Vite not starting?</summary>

Check Node.js version:
```bash
node --version
```
Should be v18 or higher.

</details>

<details>
<summary>TypeScript errors?</summary>

Ensure all dependencies are installed:
```bash
npm install
npm install -D @types/node
```

</details>

<details>
<summary>Continue not generating code?</summary>

1. Check Continue is connected (bottom right of VS Code)
2. Try a simpler prompt first
3. Restart VS Code if needed

</details>

## What's Next?

Excellent! You now have:
- 📦 A properly configured TypeScript project
- 📋 Clear requirements in your PRD
- 🔧 All necessary dependencies installed
- 🎯 A solid foundation to build upon

In the next step, you'll implement the core chat functionality with local authentication and storage.

**[→ Continue to Step 2: Local Authentication & Storage](./step-2-local-auth-storage.md)**

---

## Additional Notes

### Why These Technologies?

- **Vite**: Fastest build tool with excellent DX
- **TypeScript**: Type safety prevents runtime errors
- **lz-string**: Best compression for text in JavaScript
- **idb**: Promise-based IndexedDB wrapper
- **PWA**: Essential for offline functionality

### Performance Tips

- Keep checking bundle size: `npm run build`
- Use Chrome DevTools Network tab in 3G mode
- Test on real devices when possible

---

**[← Back to Introduction](./README.md)** | **[→ Next: Local Auth & Storage](./step-2-local-auth-storage.md)**