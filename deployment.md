# 🚀 Deployment Guide

## AO Process Deployment and Production Setup

Complete guide for deploying PermaSearch agents to the AO network and setting up production infrastructure.

## 📋 Prerequisites

### System Requirements

- **Node.js**: 18.0 or higher
- **npm**: 9.0 or higher
- **Git**: 2.30 or higher
- **Arweave Wallet**: With sufficient AR tokens
- **AO CLI Tools**: Latest version

### AO Network Access

```bash
# Install AO CLI
npm install -g @permaweb/aos

# Verify installation
aos --version

# Check AO network status
aos network status
```

### Wallet Setup

1. **Create/Import Wallet**
   ```bash
   # Generate new wallet (for testing)
   aos wallet create

   # Or import existing wallet
   aos wallet import ./path/to/wallet.json
   ```

2. **Fund Wallet**
   - Visit: https://arweave.org
   - Use faucet for test AR (optional)
   - Minimum: 0.1 AR for deployments

## 🔧 AO Process Deployment

### Step 1: Prepare Deployment Scripts

```bash
# Clone repository
git clone https://github.com/thionne/parmawebsearch.git
cd parmawebsearch

# Install dependencies
npm install

# Verify AO processes exist
ls -la ao-processes/
# Expected: agent-manager.lua data-cache.lua
```

### Step 2: Deploy Data Cache Process

```bash
# Deploy data cache agent
node scripts/deploy-data-cache.js

# Expected output:
# 📦 Deploying Data Cache Process...
# ✅ Process deployed successfully!
# 🔗 Process ID: abc123...xyz789
# 💰 Cost: 0.001 AR
```

### Step 3: Deploy Agent Manager Process

```bash
# Update agent manager with data cache process ID
# The deployment script will automatically update the Lua file

node scripts/deploy-agent-manager.js

# Expected output:
# 📦 Deploying Agent Manager Process...
# 🔗 Data Cache Process ID: abc123...xyz789
# ✅ Process deployed successfully!
# 🔗 Process ID: def456...uvw012
# 💰 Cost: 0.001 AR
```

### Step 4: Verify Deployments

```bash
# Check process status
node scripts/verify-deployment.js

# Expected output:
# 🔍 Verifying AO Process Deployments...
# ✅ Data Cache Process: Active
# ✅ Agent Manager Process: Active
# ✅ Inter-process communication: Working
```

### Step 5: Update Environment Configuration

```bash
# Update .env with deployed process IDs
# The deployment scripts should automatically update these values

cat .env
# Expected output:
# VITE_AGENT_MANAGER_PROCESS_ID=def456...uvw012
# VITE_DATA_CACHE_PROCESS_ID=abc123...xyz789
```

## 🌐 Production Deployment Options

### Option 1: Vercel (Recommended)

#### One-Click Deployment

1. **Connect Repository**
   ```bash
   # Push to GitHub first
   git add .
   git commit -m "Production deployment"
   git push origin main
   ```

2. **Deploy on Vercel**
   - Visit: https://vercel.com
   - Click "Import Project"
   - Connect your GitHub repository
   - Configure environment variables
   - Deploy automatically

3. **Configure Environment Variables**
   ```bash
   # In Vercel dashboard
   VITE_AGENT_MANAGER_PROCESS_ID=your_agent_manager_id
   VITE_DATA_CACHE_PROCESS_ID=your_data_cache_id
   NODE_ENV=production
   ```

#### Vercel Configuration

```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "installCommand": "npm install",
  "framework": "vite",
  "rewrites": [
    { "source": "/api/(.*)", "destination": "/api/$1" },
    { "source": "/(.*)", "destination": "/index.html" }
  ],
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        { "key": "X-Frame-Options", "value": "DENY" },
        { "key": "X-Content-Type-Options", "value": "nosniff" },
        { "key": "Referrer-Policy", "value": "strict-origin-when-cross-origin" }
      ]
    }
  ]
}
```

### Option 2: Netlify

#### Netlify Deployment

1. **Connect Repository**
   ```bash
   # Push to GitHub
   git push origin main
   ```

2. **Deploy on Netlify**
   - Visit: https://netlify.com
   - Click "New site from Git"
   - Connect GitHub repository
   - Configure build settings

3. **Build Configuration**
   ```
   Build command: npm run build
   Publish directory: dist
   Node version: 18
   ```

4. **Environment Variables**
   ```
   VITE_AGENT_MANAGER_PROCESS_ID=your_agent_manager_id
   VITE_DATA_CACHE_PROCESS_ID=your_data_cache_id
   NODE_ENV=production
   ```

### Option 3: Docker Deployment

#### Docker Setup

```dockerfile
# Dockerfile
FROM node:18-alpine AS base

# Install dependencies
FROM base AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Build application
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Production image
FROM base AS runner
WORKDIR /app

# Create non-root user
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 permasearch

# Copy built application
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package.json ./package.json

# Set proper permissions
RUN chown -R permasearch:nodejs /app
USER permasearch

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

# Start application
CMD ["npm", "start"]
```

#### Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  permasearch:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - VITE_AGENT_MANAGER_PROCESS_ID=${AGENT_MANAGER_PROCESS_ID}
      - VITE_DATA_CACHE_PROCESS_ID=${DATA_CACHE_PROCESS_ID}
    env_file:
      - .env.production
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./ssl:/etc/ssl/certs:ro
    depends_on:
      - permasearch
    restart: unless-stopped
```

#### Nginx Configuration

```nginx
# nginx.conf
upstream permasearch_backend {
    server permasearch:3000;
}

server {
    listen 80;
    server_name your-domain.com;

    # Redirect to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name your-domain.com;

    # SSL configuration
    ssl_certificate /etc/ssl/certs/fullchain.pem;
    ssl_certificate_key /etc/ssl/certs/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types
        text/plain
        text/css
        text/xml
        text/javascript
        application/javascript
        application/xml+rss
        application/json;

    # Static files caching
    location /assets/ {
        proxy_pass http://permasearch_backend;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # API endpoints
    location /api/ {
        proxy_pass http://permasearch_backend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;

        # Rate limiting
        limit_req zone=api burst=10 nodelay;
    }

    # Main application
    location / {
        proxy_pass http://permasearch_backend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    # Health check endpoint
    location /health {
        access_log off;
        proxy_pass http://permasearch_backend;
    }

    # Error pages
    error_page 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }
}

# Rate limiting zones
limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
limit_req_zone $binary_remote_addr zone=search:10m rate=5r/s;
```

### Option 4: Manual Server Deployment

#### VPS Deployment

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Install PM2 for process management
sudo npm install -g pm2

# Clone repository
git clone https://github.com/thionne/parmawebsearch.git
cd parmawebsearch

# Install dependencies
npm install

# Build application
npm run build

# Configure environment
cp .env.example .env
nano .env  # Add your AO process IDs

# Start with PM2
pm2 start npm --name "permasearch" -- run start
pm2 startup
pm2 save

# Configure firewall
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable

# Install SSL certificate (Let's Encrypt)
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d your-domain.com
```

## 🔄 AO Agent Management

### Monitoring AO Processes

```bash
# Check process status
aos process list

# Monitor specific process
aos monitor <AGENT_MANAGER_PROCESS_ID>

# View process logs
aos logs <AGENT_MANAGER_PROCESS_ID>

# Check process health
aos health <AGENT_MANAGER_PROCESS_ID>
```

### Updating AO Processes

```bash
# Update agent manager code
aos update <AGENT_MANAGER_PROCESS_ID> ./ao-processes/agent-manager.lua

# Update data cache code
aos update <DATA_CACHE_PROCESS_ID> ./ao-processes/data-cache.lua

# Verify updates
aos info <AGENT_MANAGER_PROCESS_ID>
```

### Scaling AO Processes

```bash
# Enable auto-scaling
aos autoscale <AGENT_MANAGER_PROCESS_ID> --min=1 --max=5 --cpu-threshold=80

# Manual scaling
aos scale <AGENT_MANAGER_PROCESS_ID> 3

# Check scaling status
aos status <AGENT_MANAGER_PROCESS_ID>
```

## 📊 Production Monitoring

### Application Monitoring

#### Install Monitoring Tools

```bash
# Install PM2 monitoring
npm install -g pm2
pm2 install pm2-server-monit

# Install log rotation
pm2 install pm2-logrotate
pm2 set pm2-logrotate:rotateInterval '0 0 * * *'

# Start monitoring dashboard
pm2 monit
```

#### Health Check Endpoints

```typescript
// src/pages/api/health.ts
import type { NextApiRequest, NextApiResponse } from 'next';

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  if (req.method !== 'GET') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  try {
    // Check AO network connectivity
    const aoHealth = await checkAOHealth();

    // Check database connectivity
    const dbHealth = await checkDatabaseHealth();

    // Check cache health
    const cacheHealth = await checkCacheHealth();

    const overallHealth = aoHealth && dbHealth && cacheHealth;

    res.status(overallHealth ? 200 : 503).json({
      status: overallHealth ? 'healthy' : 'unhealthy',
      timestamp: new Date().toISOString(),
      version: process.env.npm_package_version,
      checks: {
        ao_network: aoHealth,
        database: dbHealth,
        cache: cacheHealth
      },
      uptime: process.uptime(),
      memory: process.memoryUsage()
    });
  } catch (error) {
    res.status(500).json({
      status: 'error',
      error: error.message,
      timestamp: new Date().toISOString()
    });
  }
}

async function checkAOHealth(): Promise<boolean> {
  try {
    // Implement AO health check
    return true;
  } catch {
    return false;
  }
}

async function checkDatabaseHealth(): Promise<boolean> {
  try {
    // Implement database health check
    return true;
  } catch {
    return false;
  }
}

async function checkCacheHealth(): Promise<boolean> {
  try {
    // Implement cache health check
    return true;
  }
}
```

### AO Process Monitoring

#### Custom Monitoring Script

```javascript
// monitor-ao-agents.js
const { PermaSearch } = require('@permasearch/sdk');

class AOMonitor {
  constructor() {
    this.client = new PermaSearch();
    this.alerts = [];
  }

  async startMonitoring() {
    console.log('🔍 Starting AO Agent Monitoring...');

    // Monitor agent manager
    setInterval(() => this.checkAgentManager(), 30000);

    // Monitor data cache
    setInterval(() => this.checkDataCache(), 60000);

    // Monitor search performance
    setInterval(() => this.checkSearchPerformance(), 300000);

    // Send alerts
    setInterval(() => this.sendAlerts(), 3600000);
  }

  async checkAgentManager() {
    try {
      const status = await this.client.agents.getStatus();
      const agentManager = status.agents.find(a => a.id === process.env.VITE_AGENT_MANAGER_PROCESS_ID);

      if (!agentManager || agentManager.status !== 'active') {
        this.addAlert('Agent Manager is not active', 'critical');
      }

      if (agentManager.uptime < 0.99) {
        this.addAlert('Agent Manager uptime below 99%', 'warning');
      }
    } catch (error) {
      this.addAlert(`Agent Manager check failed: ${error.message}`, 'critical');
    }
  }

  async checkDataCache() {
    try {
      // Implement data cache health check
      const cacheStats = await this.client.cache.getStats();

      if (cacheStats.hitRate < 0.8) {
        this.addAlert('Cache hit rate below 80%', 'warning');
      }

      if (cacheStats.size > 1000000000) { // 1GB
        this.addAlert('Cache size exceeds 1GB', 'info');
      }
    } catch (error) {
      this.addAlert(`Data Cache check failed: ${error.message}`, 'critical');
    }
  }

  async checkSearchPerformance() {
    try {
      const startTime = Date.now();
      await this.client.search('test query');
      const responseTime = Date.now() - startTime;

      if (responseTime > 1000) {
        this.addAlert(`Search response time: ${responseTime}ms`, 'warning');
      }
    } catch (error) {
      this.addAlert(`Search performance check failed: ${error.message}`, 'critical');
    }
  }

  addAlert(message, severity) {
    this.alerts.push({
      message,
      severity,
      timestamp: new Date().toISOString()
    });
  }

  async sendAlerts() {
    if (this.alerts.length === 0) return;

    // Send alerts to configured endpoints
    for (const alert of this.alerts) {
      await this.sendAlertToWebhook(alert);
      await this.sendAlertToEmail(alert);
    }

    this.alerts = [];
  }

  async sendAlertToWebhook(alert) {
    if (!process.env.ALERT_WEBHOOK_URL) return;

    try {
      await fetch(process.env.ALERT_WEBHOOK_URL, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(alert)
      });
    } catch (error) {
      console.error('Failed to send webhook alert:', error);
    }
  }

  async sendAlertToEmail(alert) {
    if (!process.env.ALERT_EMAIL) return;

    // Implement email sending logic
    console.log(`📧 Alert Email: ${alert.severity.toUpperCase()} - ${alert.message}`);
  }
}

// Start monitoring
const monitor = new AOMonitor();
monitor.startMonitoring();
```

## 🔐 Security Configuration

### Production Security

#### Environment Variables

```bash
# Generate secure secrets
openssl rand -hex 32 > .env.production.secret

# Set proper permissions
chmod 600 .env.production
chmod 600 .env.production.secret

# Use environment-specific configs
cp .env.example .env.production
```

#### SSL/TLS Configuration

```bash
# Generate self-signed certificate (for testing)
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes

# Or use Let's Encrypt
sudo certbot certonly --standalone -d your-domain.com

# Configure SSL in production
# Update nginx.conf or vercel.json with SSL settings
```

#### Firewall Configuration

```bash
# UFW configuration
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable

# Verify firewall status
sudo ufw status
```

### AO Process Security

#### Process Permissions

```lua
-- ao-processes/agent-manager.lua
local PERMISSIONS = {
  -- Allowed operations
  ALLOWED_OPERATIONS = {
    "Search",
    "GetStatus",
    "HealthCheck"
  },

  -- Rate limiting
  RATE_LIMITS = {
    requests_per_minute = 60,
    burst_size = 10
  },

  -- Authentication
  REQUIRE_AUTH = true,
  ALLOWED_WALLETS = {
    -- Add trusted wallet addresses
  }
}

-- Security middleware
Handlers.add(
  "SecurityCheck",
  function(msg)
    -- Verify wallet signature
    if PERMISSIONS.REQUIRE_AUTH and not verifyWallet(msg.From) then
      return { Error = "Unauthorized" }
    end

    -- Check rate limits
    if not checkRateLimit(msg.From) then
      return { Error = "Rate limit exceeded" }
    end

    -- Check operation permissions
    if not isAllowedOperation(msg.Tags.Action) then
      return { Error = "Operation not allowed" }
    end
  end
)
```

## 📈 Performance Optimization

### Caching Strategy

#### Redis Cache Setup

```javascript
// redis-cache.js
const redis = require('redis');

class RedisCache {
  constructor() {
    this.client = redis.createClient({
      host: process.env.REDIS_HOST || 'localhost',
      port: process.env.REDIS_PORT || 6379,
      password: process.env.REDIS_PASSWORD
    });

    this.client.on('error', (err) => console.error('Redis error:', err));
  }

  async get(key) {
    try {
      const value = await this.client.get(key);
      return value ? JSON.parse(value) : null;
    } catch (error) {
      console.error('Cache get error:', error);
      return null;
    }
  }

  async set(key, value, ttl = 300) {
    try {
      await this.client.setEx(key, ttl, JSON.stringify(value));
    } catch (error) {
      console.error('Cache set error:', error);
    }
  }

  async del(key) {
    try {
      await this.client.del(key);
    } catch (error) {
      console.error('Cache del error:', error);
    }
  }

  async clear() {
    try {
      await this.client.flushAll();
    } catch (error) {
      console.error('Cache clear error:', error);
    }
  }
}

module.exports = RedisCache;
```

#### CDN Configuration

```javascript
// cdn-config.js
const CDN_CONFIG = {
  provider: 'cloudflare', // or 'aws', 'google', 'vercel'
  domain: 'cdn.permasearch.io',

  // Cache settings
  cache: {
    static_assets: {
      ttl: 31536000, // 1 year
      patterns: ['/assets/*', '/favicon.ico', '/manifest.json']
    },
    api_responses: {
      ttl: 300, // 5 minutes
      patterns: ['/api/search', '/api/content']
    }
  },

  // Compression
  compression: {
    gzip: true,
    brotli: true,
    minCompressionRatio: 0.8
  },

  // Security
  security: {
    cors: {
      origins: ['https://permasearch.io', 'https://app.permasearch.io'],
      methods: ['GET', 'POST', 'OPTIONS'],
      headers: ['Content-Type', 'Authorization']
    },
    headers: {
      'X-Frame-Options': 'DENY',
      'X-Content-Type-Options': 'nosniff',
      'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
    }
  }
};
```

## 🔄 Backup and Recovery

### Database Backup

```bash
# Automated backup script
#!/bin/bash

BACKUP_DIR="/var/backups/permasearch"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/permasearch_$DATE.tar.gz"

# Create backup directory
mkdir -p $BACKUP_DIR

# Backup application data
tar -czf $BACKUP_FILE \
  --exclude='node_modules' \
  --exclude='.git' \
  --exclude='logs' \
  /path/to/permasearch

# Upload to cloud storage
aws s3 cp $BACKUP_FILE s3://permasearch-backups/

# Cleanup old backups (keep last 7 days)
find $BACKUP_DIR -name "*.tar.gz" -mtime +7 -delete

echo "Backup completed: $BACKUP_FILE"
```

### AO Process Backup

```javascript
// ao-backup.js
const { PermaSearch } = require('@permasearch/sdk');

class AOBackup {
  constructor() {
    this.client = new PermaSearch();
    this.backupInterval = 24 * 60 * 60 * 1000; // 24 hours
  }

  async startBackupSchedule() {
    setInterval(() => this.performBackup(), this.backupInterval);
  }

  async performBackup() {
    try {
      console.log('🔄 Starting AO Process Backup...');

      // Backup agent states
      await this.backupAgentStates();

      // Backup configurations
      await this.backupConfigurations();

      // Backup metrics
      await this.backupMetrics();

      console.log('✅ AO Process Backup Completed');
    } catch (error) {
      console.error('❌ Backup failed:', error);
    }
  }

  async backupAgentStates() {
    const status = await this.client.agents.getStatus();

    for (const agent of status.agents) {
      const state = await this.client.agents.getState(agent.id);
      await this.saveToStorage(`agent_states/${agent.id}.json`, state);
    }
  }

  async backupConfigurations() {
    const configs = {
      agentManager: process.env.VITE_AGENT_MANAGER_PROCESS_ID,
      dataCache: process.env.VITE_DATA_CACHE_PROCESS_ID,
      environment: process.env.NODE_ENV,
      version: process.env.npm_package_version
    };

    await this.saveToStorage('configurations/backup.json', configs);
  }

  async backupMetrics() {
    const metrics = await this.client.analytics.getSystemMetrics();
    const timestamp = new Date().toISOString().split('T')[0];
    await this.saveToStorage(`metrics/${timestamp}.json`, metrics);
  }

  async saveToStorage(path, data) {
    // Implement storage logic (S3, Arweave, local file)
    // This could save to Arweave for permanent storage
  }
}
```

## 📊 Deployment Checklist

### Pre-Deployment
- [ ] AO processes deployed and tested
- [ ] Environment variables configured
- [ ] SSL certificates obtained
- [ ] Domain configured
- [ ] CDN set up

### Deployment
- [ ] Code deployed to production
- [ ] Database migrations run
- [ ] Cache warmed up
- [ ] Monitoring enabled
- [ ] Backup systems active

### Post-Deployment
- [ ] Health checks passing
- [ ] Performance benchmarks met
- [ ] Error monitoring active
- [ ] User acceptance testing completed
- [ ] Documentation updated

### Rollback Plan
- [ ] Previous version tagged
- [ ] Database backup available
- [ ] Rollback script ready
- [ ] Communication plan prepared

---

## 🚨 Emergency Procedures

### Service Outage Response

1. **Assess Situation**
   ```bash
   # Check service status
   curl -f https://your-domain.com/health

   # Check AO processes
   aos health <AGENT_MANAGER_PROCESS_ID>

   # Check server resources
   htop
   df -h
   ```

2. **Common Issues & Fixes**
   ```bash
   # Restart application
   pm2 restart permasearch

   # Restart AO processes
   aos restart <PROCESS_ID>

   # Clear cache
   redis-cli FLUSHALL

   # Check logs
   pm2 logs permasearch
   aos logs <PROCESS_ID>
   ```

3. **Escalation Process**
   - Level 1: Restart services
   - Level 2: Rollback deployment
   - Level 3: Contact infrastructure team
   - Level 4: Emergency alert to stakeholders

### Data Recovery

```bash
# Restore from backup
BACKUP_FILE=$(ls -t /var/backups/permasearch/*.tar.gz | head -1)
tar -xzf $BACKUP_FILE -C /tmp/restore

# Restore AO processes
node scripts/restore-ao-processes.js

# Verify restoration
npm run test:integration
```

---

## 📞 Support & Resources

**🚀 Deployment Support:**
- 📖 [Configuration Guide](configuration.md)
- 📊 [Monitoring Guide](monitoring.md)
- 🏗️ [Architecture Overview](architecture.md)

**🔗 Community Resources:**
- [AO Developer Discord](https://discord.gg/ao)
- [Arweave Developer Forums](https://community.arweave.org)
- [GitHub Issues](https://github.com/thionne/parmawebsearch/issues)

**📧 Professional Support:**
- Enterprise deployment assistance
- Custom infrastructure setup
- 24/7 monitoring services
- SLA guarantees

---

**🎯 Ready to deploy PermaSearch?** Start with our [quick start guide](quick-start.md) and join thousands of decentralized applications already running on the AO network!
