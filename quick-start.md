# ⚡ Quick Start Guide

## Get PermaSearch Running in 5 Minutes

This guide will get you up and running with PermaSearch in under 5 minutes. Perfect for developers who want to see the system in action quickly.

## 🎯 Prerequisites (1 minute)

**What you need:**
- Node.js 18+ installed
- A modern web browser
- Internet connection

**Quick verification:**
```bash
node --version  # Should show v18.0 or higher
npm --version   # Should show 9.0 or higher
```

## 🚀 5-Minute Setup

### Step 1: Clone & Install (2 minutes)

```bash
# Clone the repository
git clone https://github.com/thionne/parmawebsearch.git
cd parmawebsearch

# Install dependencies (this takes ~1-2 minutes)
npm install
```

### Step 2: Configure Environment (30 seconds)

```bash
# Copy environment template
cp .env.example .env
```

The `.env` file is pre-configured with default values. For production, you'll need to add your AO process IDs later.

### Step 3: Start the Application (30 seconds)

```bash
# Start development server
npm run dev
```

**Expected output:**
```
VITE v4.4.9  ready in 3000ms
➜  Local:   http://localhost:5173/
➜  Network: http://192.168.1.100:5173/
```

### Step 4: Open in Browser (30 seconds)

1. **Open your browser**
2. **Navigate to:** http://localhost:5173
3. **See PermaSearch homepage** ✅

## 🔍 Test the Search (1 minute)

### Basic Search Test

1. **Click the search bar** on the homepage
2. **Enter a query** like "music" or "images"
3. **Press Enter or click Search**
4. **Watch results load progressively**

### What You'll See

- **Instant loading animation** with progress bar
- **Progressive results** appearing as they're found
- **Rich content previews** with images, titles, and metadata
- **Filter options** for content types

## 🎮 Interactive Demo

### Try These Searches

```bash
# Music search
"electronic music"

# Image search
"NFT art"

# Document search
"technical documentation"

# Website search
"blockchain news"
```

### Advanced Features to Test

1. **Content Type Filtering**
   - Switch between Music, Images, Videos, Documents
   - Notice how results adapt to your selection

2. **Progressive Loading**
   - Watch the progress bar
   - See results appear in real-time
   - Notice instant cached results

3. **Responsive Design**
   - Resize your browser window
   - Test on mobile/tablet view

## 🧪 Developer Console (Optional)

### Access Developer Tools

1. **Navigate to:** http://localhost:5173/console
2. **Test API endpoints** directly
3. **View detailed logs** and responses
4. **Debug search queries**

### Quick API Test

```bash
# In browser console or terminal
curl -X POST "http://localhost:5173/api/search" \
  -H "Content-Type: application/json" \
  -d '{"query": "test", "limit": 5}'
```

## 🚀 Next Steps (After 5 Minutes)

### For Development

```bash
# Explore the codebase
code .  # Opens in VS Code

# View all available scripts
npm run

# Check for updates
npm outdated
```

### For Production Deployment

1. **Deploy AO Agents** (see deployment guide)
2. **Configure environment variables** with real process IDs
3. **Set up monitoring** and health checks
4. **Deploy to hosting platform** (Vercel, Netlify, etc.)

## 🔧 Troubleshooting

### Common Issues & Quick Fixes

**❌ "npm install" fails**
```bash
# Quick fix
npm cache clean --force
rm -rf node_modules package-lock.json
npm install
```

**❌ Port 5173 already in use**
```bash
# Use different port
npm run dev -- --port 3001
```

**❌ Page doesn't load**
```bash
# Check if server is running
curl http://localhost:5173

# Restart development server
npm run dev
```

**❌ Search returns no results**
```bash
# Check browser console for errors
# Verify internet connection
# Try different search terms
```

### Performance Tips

```bash
# For faster builds
export NODE_OPTIONS="--max-old-space-size=4096"

# For faster development
npm run dev -- --host 0.0.0.0
```

## 📚 Learn More

### Documentation Links

- **[Installation Guide](installation.md)** - Complete setup instructions
- **[API Reference](api-reference.md)** - Full API documentation
- **[Deployment Guide](deployment.md)** - Production deployment
- **[Architecture Overview](architecture.md)** - System design

### Community & Support

- **GitHub Issues**: Report bugs or request features
- **Discord Community**: Real-time help and discussion
- **Documentation Site**: Comprehensive guides and tutorials

## 🎉 You're All Set!

**Congratulations!** You've successfully:
- ✅ Installed PermaSearch
- ✅ Started the development server
- ✅ Tested the search functionality
- ✅ Explored the user interface

### What You Can Do Now

1. **Explore the interface** - Try different searches and filters
2. **Test API endpoints** - Use the developer console
3. **Customize the UI** - Modify components in `/src`
4. **Add new features** - Extend functionality
5. **Deploy to production** - Follow the deployment guide

### Quick Commands Reference

```bash
# Start development
npm run dev

# Build for production
npm run build

# Run tests
npm test

# View all scripts
npm run
```

---

**🚀 Ready to build amazing decentralized search experiences?** Let's dive deeper into the [API Reference](api-reference.md) or explore the [Architecture Overview](architecture.md)!

**Need help?** Join our [Discord community](https://discord.gg/permasearch) or create a [GitHub issue](https://github.com/thionne/parmawebsearch/issues).
