# ⚙️ Configuration Guide

## Environment and Deployment Configuration

This comprehensive guide covers all configuration options for PermaSearch, from development to production environments.

## 📁 Environment Configuration

### Core Configuration Files

#### `.env` - Main Environment Variables

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

# Performance Settings
VITE_MAX_RESULTS_PER_PAGE=20
VITE_SEARCH_TIMEOUT_MS=10000
VITE_CACHE_TTL_MINUTES=30

# Feature Flags
VITE_ENABLE_ADVANCED_SEARCH=true
VITE_ENABLE_ANALYTICS=false
VITE_ENABLE_DEBUG_LOGGING=false
```

#### `.env.example` - Configuration Template

```env
# Copy this file to .env and configure your values
# AO Network Configuration (Required for production)
VITE_AGENT_MANAGER_PROCESS_ID=
VITE_DATA_CACHE_PROCESS_ID=

# Arweave Network Settings (Optional - defaults provided)
ARWEAVE_HOST=arweave.net
ARWEAVE_PORT=443
ARWEAVE_PROTOCOL=https

# Application Configuration
VITE_APP_NAME=PermaSearch
VITE_APP_VERSION=1.0.0
NODE_ENV=development

# Performance Settings (Optional)
VITE_MAX_RESULTS_PER_PAGE=20
VITE_SEARCH_TIMEOUT_MS=10000
VITE_CACHE_TTL_MINUTES=30

# Feature Flags (Optional)
VITE_ENABLE_ADVANCED_SEARCH=true
VITE_ENABLE_ANALYTICS=false
VITE_ENABLE_DEBUG_LOGGING=false
```

### AO Process Configuration

#### Agent Manager Configuration

```lua
-- ao-processes/agent-manager.lua
local CONFIG = {
  -- Process identification
  PROCESS_ID = "your_agent_manager_process_id",

  -- Search configuration
  MAX_CONCURRENT_SEARCHES = 5,
  SEARCH_TIMEOUT_SECONDS = 30,

  -- Cache configuration
  CACHE_ENABLED = true,
  CACHE_TTL_MINUTES = 15,

  -- Monitoring configuration
  HEALTH_CHECK_INTERVAL = 300, -- 5 minutes
  LOG_LEVEL = "info",

  -- Rate limiting
  RATE_LIMIT_REQUESTS_PER_MINUTE = 60,
  RATE_LIMIT_BURST_SIZE = 10
}
```

#### Data Cache Configuration

```lua
-- ao-processes/data-cache.lua
local CONFIG = {
  -- Process identification
  PROCESS_ID = "your_data_cache_process_id",

  -- Cache configuration
  MAX_CACHE_SIZE_MB = 100,
  CACHE_CLEANUP_INTERVAL_MINUTES = 60,

  -- Content discovery
  DISCOVERY_BATCH_SIZE = 50,
  DISCOVERY_INTERVAL_MINUTES = 10,

  -- Data sources
  ENABLE_ARWEAVE_GRAPHQL = true,
  ENABLE_DIRECT_API = true,

  -- Monitoring
  METRICS_ENABLED = true,
  METRICS_RETENTION_DAYS = 7
}
```

## 🔧 Runtime Configuration

### Application Settings

#### Search Configuration

```typescript
// src/config/search.ts
export const SEARCH_CONFIG = {
  // Query settings
  MIN_QUERY_LENGTH: 2,
  MAX_QUERY_LENGTH: 200,

  // Result settings
  DEFAULT_RESULTS_PER_PAGE: 20,
  MAX_RESULTS_PER_PAGE: 100,

  // Timeout settings
  SEARCH_TIMEOUT_MS: 10000,
  CACHE_TIMEOUT_MS: 5000,

  // Content type filters
  SUPPORTED_CONTENT_TYPES: [
    'image', 'audio', 'video', 'document', 'webpage'
  ],

  // Search algorithms
  ENABLE_FUZZY_MATCHING: true,
  FUZZY_MATCH_THRESHOLD: 0.6
};
```

#### UI Configuration

```typescript
// src/config/ui.ts
export const UI_CONFIG = {
  // Theme settings
  THEME: 'dark' | 'light' | 'auto',
  PRIMARY_COLOR: '#00ff88',
  ACCENT_COLOR: '#0066ff',

  // Layout settings
  MAX_CONTENT_WIDTH: '1200px',
  SIDEBAR_WIDTH: '280px',

  // Animation settings
  ANIMATION_DURATION: 300,
  ENABLE_REDUCED_MOTION: false,

  // Loading settings
  LOADING_SPINNER_TYPE: 'pulse',
  PROGRESS_BAR_ENABLED: true
};
```

### Network Configuration

#### AO Network Settings

```typescript
// src/config/ao.ts
export const AO_CONFIG = {
  // Network endpoints
  MAINNET_ENDPOINT: 'https://arweave.net',
  TESTNET_ENDPOINT: 'https://testnet.arweave.net',

  // Process IDs
  AGENT_MANAGER_PROCESS: import.meta.env.VITE_AGENT_MANAGER_PROCESS_ID,
  DATA_CACHE_PROCESS: import.meta.env.VITE_DATA_CACHE_PROCESS_ID,

  // Connection settings
  CONNECTION_TIMEOUT_MS: 5000,
  MAX_RETRIES: 3,
  RETRY_DELAY_MS: 1000,

  // Message settings
  MAX_MESSAGE_SIZE: 1048576, // 1MB
  DEFAULT_TTL: 3600 // 1 hour
};
```

#### Arweave Integration

```typescript
// src/config/arweave.ts
export const ARWEAVE_CONFIG = {
  // Network settings
  HOST: 'arweave.net',
  PORT: 443,
  PROTOCOL: 'https',

  // Gateway settings
  GATEWAY_URL: 'https://arweave.net',
  GRAPHQL_URL: 'https://arweave.net/graphql',

  // Transaction settings
  CONFIRMATIONS_REQUIRED: 10,
  TIMEOUT_MS: 30000,

  // Content policies
  MAX_CONTENT_SIZE: 1073741824, // 1GB
  ALLOWED_CONTENT_TYPES: [
    'image/*', 'audio/*', 'video/*', 'text/*', 'application/pdf'
  ]
};
```

## 🏭 Production Configuration

### Environment Variables for Production

```env
# Production Environment
NODE_ENV=production
VITE_APP_ENV=production

# AO Production Processes
VITE_AGENT_MANAGER_PROCESS_ID=AkMgLZQVxOUCpN4K9tsgdczD5iDvpbC53z3fUFpN0yY
VITE_DATA_CACHE_PROCESS_ID=rHln4O76lKmXpNFnI6lf_aHwR0YqGvLbEOKMuGJ4Fvg

# Production Database/API Keys
DATABASE_URL=postgresql://...
REDIS_URL=redis://...
ANALYTICS_KEY=your_analytics_key

# Security Settings
ENABLE_HTTPS=true
SESSION_SECRET=your_secure_session_secret
API_RATE_LIMIT=1000

# Performance Settings
ENABLE_CACHING=true
CACHE_BACKEND=redis
CDN_URL=https://cdn.permasearch.io

# Monitoring
ENABLE_METRICS=true
METRICS_ENDPOINT=https://metrics.permasearch.io
LOG_LEVEL=warn
```

### Docker Configuration

#### Dockerfile

```dockerfile
# Production Dockerfile
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

# Set permissions
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

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    restart: unless-stopped

volumes:
  redis_data:
```

### Nginx Configuration

```nginx
# nginx.conf
upstream permasearch_app {
    server app:3000;
}

server {
    listen 80;
    server_name permasearch.io;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css text/xml text/javascript application/javascript application/xml+rss application/json;

    # Static files
    location /_next/static {
        proxy_pass http://permasearch_app;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # API routes
    location /api {
        proxy_pass http://permasearch_app;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    # Main application
    location / {
        proxy_pass http://permasearch_app;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    # Health check
    location /health {
        access_log off;
        return 200 "healthy\n";
        add_header Content-Type text/plain;
    }
}
```

## 🔒 Security Configuration

### Environment Security

```bash
# Generate secure secrets
openssl rand -hex 32 > .env.secret
openssl rand -base64 32 > session_secret.txt

# Set proper permissions
chmod 600 .env
chmod 600 .env.secret
chmod 600 session_secret.txt
```

### API Security

```typescript
// src/config/security.ts
export const SECURITY_CONFIG = {
  // CORS settings
  CORS_ORIGINS: ['https://permasearch.io', 'https://app.permasearch.io'],
  CORS_METHODS: ['GET', 'POST', 'PUT', 'DELETE'],
  CORS_HEADERS: ['Content-Type', 'Authorization'],

  // Rate limiting
  RATE_LIMIT_WINDOW_MS: 15 * 60 * 1000, // 15 minutes
  RATE_LIMIT_MAX_REQUESTS: 100,

  // Authentication
  JWT_SECRET: process.env.JWT_SECRET,
  JWT_EXPIRES_IN: '24h',

  // Encryption
  ENCRYPTION_KEY: process.env.ENCRYPTION_KEY,
  ENCRYPTION_ALGORITHM: 'aes-256-gcm'
};
```

### Monitoring Security

```typescript
// src/config/monitoring.ts
export const MONITORING_CONFIG = {
  // Log settings
  LOG_LEVEL: process.env.LOG_LEVEL || 'info',
  LOG_FORMAT: 'json',

  // Metrics
  METRICS_ENABLED: true,
  METRICS_ENDPOINT: process.env.METRICS_ENDPOINT,

  // Alerts
  ALERT_EMAIL: process.env.ALERT_EMAIL,
  ALERT_WEBHOOK: process.env.ALERT_WEBHOOK,

  // Security monitoring
  ENABLE_SECURITY_LOGGING: true,
  SENSITIVE_DATA_MASKING: true
};
```

## 📊 Monitoring Configuration

### Application Monitoring

```typescript
// src/config/monitoring.ts
export const APP_MONITORING = {
  // Performance monitoring
  ENABLE_PERFORMANCE_MONITORING: true,
  PERFORMANCE_SAMPLE_RATE: 0.1,

  // Error tracking
  ENABLE_ERROR_TRACKING: true,
  ERROR_REPORTING_ENDPOINT: process.env.ERROR_REPORTING_ENDPOINT,

  // User analytics
  ENABLE_ANALYTICS: process.env.ENABLE_ANALYTICS === 'true',
  ANALYTICS_ENDPOINT: process.env.ANALYTICS_ENDPOINT,

  // Real-time metrics
  ENABLE_REAL_TIME_METRICS: true,
  METRICS_UPDATE_INTERVAL: 5000
};
```

### AO Process Monitoring

```lua
-- AO Process monitoring configuration
local MONITORING_CONFIG = {
  -- Health checks
  HEALTH_CHECK_ENABLED = true,
  HEALTH_CHECK_INTERVAL = 60, -- seconds

  -- Performance metrics
  PERFORMANCE_MONITORING = true,
  METRICS_RETENTION = 7, -- days

  -- Alert thresholds
  CPU_THRESHOLD = 80, -- percent
  MEMORY_THRESHOLD = 90, -- percent
  RESPONSE_TIME_THRESHOLD = 5000 -- milliseconds
}
```

## 🔧 Advanced Configuration

### Custom Search Algorithms

```typescript
// src/config/search-algorithms.ts
export const SEARCH_ALGORITHMS = {
  // Fuzzy matching
  FUZZY_MATCHING: {
    enabled: true,
    threshold: 0.6,
    maxDistance: 3
  },

  // Semantic search
  SEMANTIC_SEARCH: {
    enabled: true,
    model: 'text-embedding-ada-002',
    similarityThreshold: 0.8
  },

  // Content ranking
  CONTENT_RANKING: {
    recencyWeight: 0.3,
    relevanceWeight: 0.5,
    popularityWeight: 0.2
  }
};
```

### Database Configuration

```typescript
// src/config/database.ts
export const DATABASE_CONFIG = {
  // Connection settings
  HOST: process.env.DB_HOST || 'localhost',
  PORT: parseInt(process.env.DB_PORT || '5432'),
  DATABASE: process.env.DB_NAME || 'permasearch',
  USERNAME: process.env.DB_USER || 'permasearch',
  PASSWORD: process.env.DB_PASSWORD,

  // Pool settings
  MIN_CONNECTIONS: 2,
  MAX_CONNECTIONS: 20,
  CONNECTION_TIMEOUT: 30000,

  // Query optimization
  ENABLE_QUERY_LOGGING: process.env.NODE_ENV === 'development',
  SLOW_QUERY_THRESHOLD: 1000
};
```

## 📋 Configuration Validation

### Configuration Validator

```typescript
// src/utils/config-validator.ts
export class ConfigValidator {
  static validateEnvironment() {
    const required = [
      'VITE_AGENT_MANAGER_PROCESS_ID',
      'VITE_DATA_CACHE_PROCESS_ID'
    ];

    const missing = required.filter(key => !process.env[key]);

    if (missing.length > 0) {
      throw new Error(`Missing required environment variables: ${missing.join(', ')}`);
    }

    console.log('✅ Environment configuration validated');
  }

  static validateAOConnection() {
    // Validate AO process connectivity
    // Implementation details...
  }
}

// Validate on startup
ConfigValidator.validateEnvironment();
```

---

## 🚀 Configuration Summary

| Configuration Type | File | Purpose | Environment |
|-------------------|------|---------|-------------|
| **Environment Variables** | `.env` | Core settings | All |
| **AO Process Config** | `agent-manager.lua` | Agent behavior | AO Network |
| **Application Config** | `src/config/` | App settings | Client |
| **Security Config** | `src/config/security.ts` | Security policies | Server |
| **Docker Config** | `docker-compose.yml` | Container setup | Production |
| **Nginx Config** | `nginx.conf` | Web server | Production |

**🎯 Next Steps:**
1. Review your current configuration
2. Update environment variables for your deployment
3. Test configuration validation
4. Deploy with confidence!

**📞 Need Help?** Check the [troubleshooting guide](troubleshooting.md) or join our [community Discord](https://discord.gg/permasearch).
