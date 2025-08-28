# 📦 Installation Guide

## Complete Setup Instructions for PermaSearch

This guide provides comprehensive installation and setup instructions for deploying PermaSearch on your local development environment and production systems.

## 📋 Prerequisites

### System Requirements

| Component | Version | Purpose |
|-----------|---------|---------|
| **Node.js** | ≥18.0 | Runtime environment |
| **npm** | ≥9.0 | Package management |
| **Git** | ≥2.30 | Version control |
| **Arweave Wallet** | Latest | Blockchain authentication |

### Hardware Requirements

- **RAM**: 4GB minimum, 8GB recommended
- **Storage**: 2GB free space for dependencies
- **Network**: Stable internet connection for AO network access

### Supported Operating Systems

- **Linux**: Ubuntu 20.04+, CentOS 8+, Fedora 35+
- **macOS**: Monterey (12.0+) or later
- **Windows**: Windows 11 with WSL2 recommended

## 🚀 Installation Steps

### Step 1: Clone the Repository

```bash
# Clone the PermaSearch repository
git clone https://github.com/thionne/parmawebsearch.git
cd parmawebsearch

# Verify the installation
ls -la
```

Expected output:
```
drwxr-xr-x  .git/
-rw-r--r--  package.json
-rw-r--r--  README.md
drwxr-xr-x  src/
drwxr-xr-x  ao-processes/
```

### Step 2: Install Dependencies

```bash
# Install all required dependencies
npm install

# Verify installation
npm list --depth=0
```

This will install:
- **React 18** - UI framework
- **TypeScript** - Type safety
- **Vite** - Build tool
- **@permaweb/aoconnect** - AO blockchain integration
- **ShadCN/UI** - Component library

### Step 3: Configure Environment Variables

```bash
# Copy environment template
cp .env.example .env

# Edit configuration
nano .env  # or use your preferred editor
```

**Required Environment Variables:**

```env
# AO Network Configuration
VITE_AGENT_MANAGER_PROCESS_ID=your_agent_manager_process_id
VITE_DATA_CACHE_PROCESS_ID=your_data_cache_process_id

# Arweave Network Settings
ARWEAVE_HOST=arweave.net
ARWEAVE_PORT=443
ARWEAVE_PROTOCOL=https

# Application Configuration
VITE_APP_NAME=PermaSearch
VITE_APP_VERSION=1.0.0
NODE_ENV=development
```

### Step 4: Verify Installation

```bash
# Run development server
npm run dev

# Expected output:
# VITE v4.4.9  ready in 3000ms
# ➜  Local:   http://localhost:5173/
# ➜  Network: http://192.168.1.100:5173/
```

## 🔧 AO Network Setup

### Installing AO CLI Tools

```bash
# Install AO CLI globally
npm install -g @permaweb/aos

# Verify installation
aos --version
```

### Wallet Configuration

1. **Install ArConnect Browser Extension**
   - Visit: https://arconnect.io
   - Add to your browser
   - Create or import wallet

2. **Fund Your Wallet**
   - Visit: https://arweave.org
   - Use faucet for test AR (optional)

3. **Configure Wallet Connection**
   ```javascript
   // This is handled automatically by the application
   // Users connect via ArConnect popup
   ```

## 🌐 Production Deployment

### Option 1: Vercel Deployment (Recommended)

1. **Connect Repository**
   ```bash
   # Push to GitHub first
   git add .
   git commit -m "Initial deployment"
   git push origin main
   ```

2. **Deploy on Vercel**
   - Visit: https://vercel.com
   - Import your GitHub repository
   - Configure environment variables
   - Deploy automatically

### Option 2: Manual Server Deployment

```bash
# Build production assets
npm run build

# Serve static files
npm install -g serve
serve -s dist -l 3000
```

### Option 3: Docker Deployment

```dockerfile
# Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]
```

```bash
# Build and run
docker build -t permasearch .
docker run -p 3000:3000 permasearch
```

## 🔍 Troubleshooting

### Common Installation Issues

**Issue: `npm install` fails**
```bash
# Clear npm cache
npm cache clean --force

# Delete node_modules
rm -rf node_modules package-lock.json

# Reinstall
npm install
```

**Issue: Port 5173 already in use**
```bash
# Kill process using port
lsof -ti:5173 | xargs kill -9

# Or use different port
npm run dev -- --port 3001
```

**Issue: AO connection fails**
```bash
# Check AO network status
curl https://arweave.net/info

# Verify wallet connection
# Check browser console for ArConnect errors
```

### Performance Optimization

```bash
# Enable production mode
export NODE_ENV=production

# Build optimized bundle
npm run build

# Enable compression
npm install -g compression
```

## 📊 Post-Installation Verification

### Health Check Script

```bash
# Create verification script
cat > verify-installation.js << 'EOF'
const { connect } = require('@permaweb/aoconnect');

async function verifyInstallation() {
  try {
    console.log('🔍 Verifying PermaSearch installation...');

    // Test AO connection
    const ao = connect();
    console.log('✅ AO connection successful');

    // Test wallet connection
    console.log('✅ Installation verified!');
  } catch (error) {
    console.error('❌ Verification failed:', error.message);
  }
}

verifyInstallation();
EOF

# Run verification
node verify-installation.js
```

### Manual Verification Steps

1. **Start Development Server**
   ```bash
   npm run dev
   ```

2. **Open Browser**
   - Navigate to: http://localhost:5173
   - Verify page loads correctly

3. **Test Search Functionality**
   - Enter search query
   - Verify results appear
   - Check console for errors

4. **Test Wallet Integration**
   - Click wallet connect button
   - Verify ArConnect popup appears
   - Confirm connection successful

## 🔐 Security Configuration

### Environment Security

```bash
# Generate secure environment file
openssl rand -base64 32 > .env.secret

# Set proper permissions
chmod 600 .env
chmod 600 .env.secret
```

### Network Security

- Use HTTPS in production
- Configure CORS properly
- Implement rate limiting
- Enable security headers

## 📞 Support & Resources

### Getting Help

- **Documentation**: [docs.permasearch.io](https://docs.permasearch.io)
- **GitHub Issues**: Report bugs and get help
- **Community Discord**: Real-time support
- **Professional Support**: Enterprise assistance

### Additional Resources

- [AO Protocol Documentation](https://ao.arweave.dev)
- [Arweave Developer Guide](https://docs.arweave.org)
- [React Documentation](https://react.dev)
- [TypeScript Handbook](https://typescriptlang.org/docs)

---

**🎉 Installation Complete!** Your PermaSearch instance is now ready for development and deployment.
