# 📡 API Reference

## Complete PermaSearch API Specification

This comprehensive API reference provides detailed documentation for all PermaSearch API endpoints, including request/response formats, authentication, and usage examples.

## 🔐 Authentication

All API requests require wallet-based authentication using Arweave-compatible wallets.

### Authentication Headers

```http
Authorization: Bearer <wallet-signature>
Content-Type: application/json
X-API-Key: <optional-api-key>
```

### Wallet Connection

```javascript
// Connect wallet (handled automatically by frontend)
import { connect } from '@permaweb/aoconnect';

const ao = connect();
// Wallet connection is managed by the application UI
```

## 🌐 Base URLs

| Environment | Base URL |
|-------------|----------|
| **Production** | `https://ar://permasearch/v1` |
| **Development** | `http://localhost:5173/api` |
| **Staging** | `https://staging.permasearch.io/api` |

## 📋 Core Endpoints

### Search Operations

#### POST `/v1/search`

**Primary search endpoint with advanced filtering and progressive loading capabilities.**

**Request:**
```http
POST /v1/search
Authorization: Bearer <wallet-signature>
Content-Type: application/json

{
  "query": "electronic music",
  "assetType": "audio",
  "limit": 20,
  "offset": 0,
  "filters": {
    "dateRange": {
      "start": "2024-01-01",
      "end": "2024-12-31"
    },
    "minSize": 1000000,
    "maxSize": 50000000,
    "creator": "wallet_address"
  },
  "sortBy": "relevance",
  "sortOrder": "desc"
}
```

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | ✅ | Search terms (2-200 characters) |
| `assetType` | string | ❌ | Content type: `image`, `audio`, `video`, `document`, `webpage` |
| `limit` | number | ❌ | Results per page (1-100, default: 20) |
| `offset` | number | ❌ | Pagination offset (default: 0) |
| `filters` | object | ❌ | Advanced filtering options |
| `sortBy` | string | ❌ | Sort field: `relevance`, `date`, `size`, `creator` |
| `sortOrder` | string | ❌ | Sort order: `asc`, `desc` (default: `desc`) |

**Response:**
```json
{
  "success": true,
  "data": {
    "results": [
      {
        "id": "transaction_id",
        "title": "Electronic Music Track",
        "description": "High-quality electronic music composition",
        "assetType": "audio",
        "creator": "wallet_address",
        "createdAt": "2024-01-15T10:30:00Z",
        "size": 5242880,
        "tags": [
          {"name": "music", "value": "electronic"},
          {"name": "genre", "value": "techno"}
        ],
        "contentUrl": "https://arweave.net/transaction_id",
        "thumbnailUrl": "https://arweave.net/thumbnail_tx_id",
        "metadata": {
          "duration": "4:23",
          "bitrate": "320kbps",
          "format": "mp3"
        },
        "relevanceScore": 0.95
      }
    ],
    "total": 1,
    "hasMore": false,
    "searchTime": 245,
    "cacheHit": false
  },
  "meta": {
    "requestId": "req_abc123",
    "timestamp": "2024-01-15T10:30:00Z",
    "version": "1.0.0"
  }
}
```

**Error Response:**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Query parameter is required",
    "details": {
      "field": "query",
      "value": ""
    }
  },
  "meta": {
    "requestId": "req_abc123",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

#### GET `/v1/search/suggestions`

**Get search suggestions and autocomplete options.**

```http
GET /v1/search/suggestions?q=elect&limit=5
```

**Response:**
```json
{
  "success": true,
  "data": {
    "suggestions": [
      "electronic music",
      "electronic art",
      "electronic documents"
    ],
    "popularQueries": [
      {"query": "music", "count": 1250},
      {"query": "images", "count": 980}
    ]
  }
}
```

### Content Operations

#### GET `/v1/content/{transactionId}`

**Retrieve detailed information about specific content.**

```http
GET /v1/content/abc123...xyz789
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "abc123...xyz789",
    "title": "Content Title",
    "description": "Content description",
    "assetType": "image",
    "creator": "wallet_address",
    "createdAt": "2024-01-15T10:30:00Z",
    "size": 2097152,
    "tags": [...],
    "contentUrl": "https://arweave.net/abc123...xyz789",
    "metadata": {...},
    "transactions": [
      {
        "id": "abc123...xyz789",
        "type": "data",
        "block": {
          "height": 1234567,
          "timestamp": "2024-01-15T10:30:00Z"
        }
      }
    ]
  }
}
```

#### GET `/v1/content/{transactionId}/related`

**Get related content based on tags and metadata.**

```http
GET /v1/content/abc123...xyz789/related?limit=5
```

### NFT Collection Operations

#### GET `/v1/nft/collections`

**Search and browse NFT collections.**

```http
GET /v1/nft/collections?query=dumdumz&limit=10&offset=0
```

**Response:**
```json
{
  "success": true,
  "data": {
    "collections": [
      {
        "id": "collection_tx_id",
        "name": "DumDumz",
        "description": "Unique NFT collection",
        "creator": "wallet_address",
        "totalItems": 1000,
        "floorPrice": "0.1",
        "thumbnailUrl": "https://arweave.net/thumbnail_tx_id",
        "tags": [...],
        "createdAt": "2024-01-01T00:00:00Z",
        "lastUpdated": "2024-01-15T10:30:00Z"
      }
    ],
    "total": 1,
    "hasMore": false
  }
}
```

#### GET `/v1/nft/collection/{collectionId}`

**Get detailed information about a specific NFT collection.**

```http
GET /v1/nft/collection/JAHF1fo4MECRZZFKGcT0B6XM94Lqe-3FtB4Ht_kTEK0
```

#### GET `/v1/nft/collection/{collectionId}/items`

**Get items from a specific NFT collection.**

```http
GET /v1/nft/collection/JAHF1fo4MECRZZFKGcT0B6XM94Lqe-3FtB4Ht_kTEK0/items?limit=20&offset=0
```

### Agent Operations

#### POST `/v1/agents/query`

**Send queries to autonomous agents.**

```http
POST /v1/agents/query
Authorization: Bearer <wallet-signature>
Content-Type: application/json

{
  "agentId": "content_discovery_agent",
  "message": "Find electronic music tracks from 2024",
  "sessionId": "optional_session_id",
  "options": {
    "maxResults": 20,
    "includeMetadata": true,
    "timeout": 30000
  }
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "sessionId": "session_abc123",
    "agentId": "content_discovery_agent",
    "response": {
      "type": "search_results",
      "data": [...],
      "metadata": {
        "searchTime": 1250,
        "resultsFound": 15,
        "cacheHit": false
      }
    },
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

#### GET `/v1/agents/status`

**Check agent health and status.**

```http
GET /v1/agents/status
```

**Response:**
```json
{
  "success": true,
  "data": {
    "agents": [
      {
        "id": "content_discovery_agent",
        "name": "Content Discovery Agent",
        "status": "active",
        "lastActive": "2024-01-15T10:29:45Z",
        "uptime": "99.9%",
        "version": "1.0.0"
      }
    ],
    "system": {
      "overallHealth": "healthy",
      "activeAgents": 3,
      "totalAgents": 3
    }
  }
}
```

### Analytics Operations

#### GET `/v1/analytics/search`

**Get search analytics and metrics.**

```http
GET /v1/analytics/search?period=7d&limit=10
```

**Response:**
```json
{
  "success": true,
  "data": {
    "period": "7d",
    "metrics": {
      "totalSearches": 15420,
      "uniqueQueries": 2840,
      "averageResponseTime": 245,
      "cacheHitRate": 0.85
    },
    "popularQueries": [
      {"query": "music", "count": 1250},
      {"query": "images", "count": 980}
    ],
    "contentTypeBreakdown": {
      "image": 45.2,
      "audio": 32.1,
      "video": 15.3,
      "document": 7.4
    }
  }
}
```

### System Operations

#### GET `/v1/health`

**System health check endpoint.**

```http
GET /v1/health
```

**Response:**
```json
{
  "success": true,
  "data": {
    "status": "healthy",
    "timestamp": "2024-01-15T10:30:00Z",
    "version": "1.0.0",
    "services": {
      "database": "healthy",
      "cache": "healthy",
      "ao_network": "healthy",
      "arweave": "healthy"
    },
    "metrics": {
      "uptime": "99.9%",
      "response_time": 12,
      "memory_usage": "45%"
    }
  }
}
```

#### GET `/v1/system/info`

**Get system information and configuration.**

```http
GET /v1/system/info
```

**Response:**
```json
{
  "success": true,
  "data": {
    "version": "1.0.0",
    "environment": "production",
    "build": {
      "commit": "abc123def",
      "date": "2024-01-15T08:00:00Z"
    },
    "configuration": {
      "maxResultsPerPage": 100,
      "defaultTimeout": 30000,
      "cacheEnabled": true
    },
    "ao_processes": {
      "agent_manager": "AkMgLZQVxOUCpN4K9tsgdczD5iDvpbC53z3fUFpN0yY",
      "data_cache": "rHln4O76lKmXpNFnI6lf_aHwR0YqGvLbEOKMuGJ4Fvg"
    }
  }
}
```

## 📊 Response Codes

| Code | Description |
|------|-------------|
| **200** | Success |
| **201** | Created |
| **400** | Bad Request |
| **401** | Unauthorized |
| **403** | Forbidden |
| **404** | Not Found |
| **422** | Validation Error |
| **429** | Rate Limited |
| **500** | Internal Server Error |
| **502** | Bad Gateway |
| **503** | Service Unavailable |

## 🚦 Rate Limiting

### Rate Limits

- **Authenticated requests**: 1000 requests per hour
- **Unauthenticated requests**: 100 requests per hour
- **Search requests**: 60 requests per minute
- **Agent queries**: 30 requests per minute

### Rate Limit Headers

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
X-RateLimit-Retry-After: 3600
```

## 🔧 SDK Usage

### JavaScript SDK

```javascript
import { PermaSearch } from '@permasearch/sdk';

const client = new PermaSearch({
  apiKey: 'your-api-key', // optional
  baseUrl: 'https://ar://permasearch/v1'
});

// Search with authentication
const results = await client.search({
  query: 'electronic music',
  assetType: 'audio',
  limit: 20
});

console.log(results);
```

### TypeScript SDK

```typescript
import { PermaSearch, SearchOptions } from '@permasearch/sdk';

const client = new PermaSearch({
  baseUrl: 'https://ar://permasearch/v1'
});

const searchOptions: SearchOptions = {
  query: 'NFT art',
  assetType: 'image',
  limit: 10,
  filters: {
    dateRange: {
      start: '2024-01-01',
      end: '2024-12-31'
    }
  }
};

const results = await client.search(searchOptions);
```

## 🧪 Testing

### Test Endpoints

```bash
# Health check
curl https://ar://permasearch/v1/health

# Simple search
curl -X POST "https://ar://permasearch/v1/search" \
  -H "Content-Type: application/json" \
  -d '{"query": "test", "limit": 5}'

# With authentication
curl -X POST "https://ar://permasearch/v1/search" \
  -H "Authorization: Bearer YOUR_WALLET_SIGNATURE" \
  -H "Content-Type: application/json" \
  -d '{"query": "music", "assetType": "audio"}'
```

### Error Handling

```javascript
try {
  const results = await client.search({ query: 'test' });
  console.log('Search successful:', results);
} catch (error) {
  if (error.status === 429) {
    console.log('Rate limited. Retry after:', error.headers['retry-after']);
  } else if (error.status === 401) {
    console.log('Authentication required');
  } else {
    console.error('Search failed:', error.message);
  }
}
```

## 📈 Webhooks

### Webhook Configuration

```http
POST /v1/webhooks
Authorization: Bearer <wallet-signature>
Content-Type: application/json

{
  "url": "https://your-app.com/webhooks/permasearch",
  "events": ["search.completed", "content.created"],
  "secret": "your-webhook-secret"
}
```

### Webhook Events

| Event | Description | Payload |
|-------|-------------|---------|
| `search.completed` | Search operation finished | Search results |
| `content.created` | New content indexed | Content metadata |
| `agent.response` | Agent query response | Agent response |
| `system.health` | Health status change | System status |

### Webhook Payload Example

```json
{
  "event": "search.completed",
  "timestamp": "2024-01-15T10:30:00Z",
  "data": {
    "query": "electronic music",
    "results": [...],
    "metadata": {
      "requestId": "req_abc123",
      "searchTime": 245
    }
  }
}
```

---

## 📚 Additional Resources

- **[Quick Start Guide](quick-start.md)** - Get running in 5 minutes
- **[SDK Documentation](sdk.md)** - Client library usage
- **[Integration Guide](integration.md)** - Third-party integrations
- **[Architecture Overview](architecture.md)** - System design

## 📞 Support

**Need help with the API?**
- Check the [troubleshooting guide](troubleshooting.md)
- Join our [Discord community](https://discord.gg/permasearch)
- Create a [GitHub issue](https://github.com/thionne/parmawebsearch/issues)

---

**🔗 API Base URL:** `https://ar://permasearch/v1`

**📧 Support Email:** api-support@permasearch.io

**🐙 GitHub:** [github.com/thionne/parmawebsearch](https://github.com/thionne/parmawebsearch)
