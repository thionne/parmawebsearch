# 📚 SDK Documentation

## PermaSearch Client Library Usage

Complete documentation for integrating PermaSearch into your applications using our official SDKs.

## 🚀 Installation

### JavaScript/TypeScript

```bash
# npm
npm install @permasearch/sdk

# yarn
yarn add @permasearch/sdk

# pnpm
pnpm add @permasearch/sdk
```

### Browser (CDN)

```html
<!-- Latest version -->
<script src="https://cdn.permasearch.io/sdk/v1/permasearch.min.js"></script>

<!-- Specific version -->
<script src="https://cdn.permasearch.io/sdk/v1.0.0/permasearch.min.js"></script>
```

## 🔧 Quick Setup

### Basic Initialization

```javascript
import { PermaSearch } from '@permasearch/sdk';

// Initialize with default settings
const client = new PermaSearch();

// Or with custom configuration
const client = new PermaSearch({
  baseUrl: 'https://ar://permasearch/v1',
  timeout: 10000,
  retries: 3
});
```

### With API Key (Optional)

```javascript
const client = new PermaSearch({
  apiKey: 'your-api-key',
  baseUrl: 'https://ar://permasearch/v1'
});
```

## 🔍 Core Functionality

### Basic Search

```javascript
// Simple text search
const results = await client.search('electronic music');

console.log(`Found ${results.total} results`);
results.results.forEach(result => {
  console.log(`${result.title} - ${result.creator}`);
});
```

### Advanced Search with Filters

```javascript
const results = await client.search({
  query: 'NFT art',
  assetType: 'image',
  limit: 20,
  offset: 0,
  filters: {
    dateRange: {
      start: '2024-01-01',
      end: '2024-12-31'
    },
    minSize: 1000000, // 1MB
    maxSize: 10000000, // 10MB
    creator: 'wallet_address'
  },
  sortBy: 'relevance',
  sortOrder: 'desc'
});
```

### Content Type Specific Searches

```javascript
// Music search
const music = await client.search({
  query: 'electronic',
  assetType: 'audio'
});

// Image search
const images = await client.search({
  query: 'digital art',
  assetType: 'image'
});

// Video search
const videos = await client.search({
  query: 'tutorials',
  assetType: 'video'
});

// Document search
const documents = await client.search({
  query: 'research papers',
  assetType: 'document'
});
```

### Real-time Search with Callbacks

```javascript
// Progressive search with real-time updates
const searchStream = client.searchProgressive({
  query: 'blockchain',
  assetType: 'document'
});

searchStream.on('result', (result) => {
  console.log('New result:', result.title);
});

searchStream.on('complete', (summary) => {
  console.log(`Search complete: ${summary.totalResults} found`);
});

searchStream.on('error', (error) => {
  console.error('Search error:', error);
});
```

## 🎨 NFT Collection Operations

### Browse Collections

```javascript
// Search NFT collections
const collections = await client.nft.getCollections({
  query: 'Bazar',
  limit: 10,
  sortBy: 'popularity'
});

collections.forEach(collection => {
  console.log(`${collection.name}: ${collection.totalItems} items`);
});
```

### Get Collection Details

```javascript
// Get specific collection
const collection = await client.nft.getCollection('JAHF1fo4MECRZZFKGcT0B6XM94Lqe-3FtB4Ht_kTEK0');

console.log(collection.name);
console.log(`Floor price: ${collection.floorPrice} AR`);
console.log(`Total items: ${collection.totalItems}`);
```

### Collection Items

```javascript
// Get items from collection
const items = await client.nft.getCollectionItems('JAHF1fo4MECRZZFKGcT0B6XM94Lqe-3FtB4Ht_kTEK0', {
  limit: 20,
  offset: 0,
  sortBy: 'price',
  sortOrder: 'asc'
});

items.forEach(item => {
  console.log(`${item.title} - ${item.price} AR`);
});
```

## 🤖 Agent Operations

### Query Agents

```javascript
// Query content discovery agent
const response = await client.agents.query({
  agentId: 'content_discovery_agent',
  message: 'Find trending music from 2024',
  options: {
    maxResults: 20,
    includeMetadata: true,
    timeout: 30000
  }
});

console.log('Agent response:', response.data);
```

### Agent Status

```javascript
// Check agent health
const status = await client.agents.getStatus();

status.agents.forEach(agent => {
  console.log(`${agent.name}: ${agent.status} (${agent.uptime})`);
});
```

### Custom Agent Interactions

```javascript
// Start agent conversation
const session = await client.agents.startSession('content_discovery_agent');

// Send multiple queries
await session.send('Find electronic music');
await session.send('Filter by genre: techno');

const finalResults = await session.getResults();
console.log('Conversation results:', finalResults);

// End session
await session.end();
```

## 📊 Analytics and Insights

### Search Analytics

```javascript
// Get search metrics
const analytics = await client.analytics.getSearchMetrics({
  period: '7d',
  limit: 10
});

console.log('Popular queries:');
analytics.popularQueries.forEach(query => {
  console.log(`${query.query}: ${query.count} searches`);
});
```

### Content Analytics

```javascript
// Get content type breakdown
const breakdown = await client.analytics.getContentBreakdown({
  period: '30d'
});

console.log('Content distribution:');
Object.entries(breakdown).forEach(([type, percentage]) => {
  console.log(`${type}: ${percentage}%`);
});
```

## 🔐 Authentication & Security

### Wallet Authentication

```javascript
// Automatic wallet detection
const client = new PermaSearch();

// Manual wallet connection
await client.connectWallet();

// Check authentication status
const isAuthenticated = client.isAuthenticated();
console.log('Authenticated:', isAuthenticated);

// Get wallet address
const walletAddress = client.getWalletAddress();
console.log('Wallet:', walletAddress);
```

### API Key Authentication

```javascript
// For server-side applications
const client = new PermaSearch({
  apiKey: process.env.PERMASEARCH_API_KEY,
  baseUrl: 'https://ar://permasearch/v1'
});
```

### Secure Requests

```javascript
// All requests are automatically signed
const results = await client.search('secure query');

// Custom signature for advanced use cases
const signedRequest = await client.signRequest({
  query: 'sensitive search',
  signature: customSignature
});
```

## ⚡ Advanced Features

### Caching

```javascript
// Enable intelligent caching
const client = new PermaSearch({
  cache: {
    enabled: true,
    ttl: 300000, // 5 minutes
    maxSize: 100 // max cached results
  }
});

// Cache will automatically handle duplicate requests
const results1 = await client.search('popular query');
const results2 = await client.search('popular query'); // Served from cache
```

### Rate Limiting

```javascript
// Automatic rate limiting
const client = new PermaSearch({
  rateLimit: {
    requestsPerMinute: 60,
    burstSize: 10
  }
});

// SDK handles rate limiting automatically
const results = await client.search('rate limited query');
```

### Error Handling

```javascript
try {
  const results = await client.search('test query');
} catch (error) {
  switch (error.code) {
    case 'RATE_LIMITED':
      console.log('Rate limited, retry after:', error.retryAfter);
      break;
    case 'AUTHENTICATION_FAILED':
      console.log('Please connect wallet');
      break;
    case 'NETWORK_ERROR':
      console.log('Network issue, retrying...');
      break;
    default:
      console.error('Search failed:', error.message);
  }
}
```

### Retry Logic

```javascript
// Custom retry configuration
const client = new PermaSearch({
  retry: {
    attempts: 3,
    delay: 1000,
    backoff: 'exponential'
  }
});

// Automatic retry on network failures
const results = await client.search('reliable query');
```

## 🎯 Framework Integrations

### React Hook

```javascript
import { usePermaSearch } from '@permasearch/react';

function SearchComponent() {
  const {
    search,
    results,
    loading,
    error,
    hasMore,
    loadMore
  } = usePermaSearch();

  const handleSearch = async (query) => {
    await search({ query, assetType: 'image' });
  };

  return (
    <div>
      <input
        onChange={(e) => handleSearch(e.target.value)}
        placeholder="Search images..."
      />

      {loading && <div>Loading...</div>}
      {error && <div>Error: {error.message}</div>}

      {results.map(result => (
        <div key={result.id}>
          <img src={result.thumbnailUrl} alt={result.title} />
          <h3>{result.title}</h3>
        </div>
      ))}

      {hasMore && (
        <button onClick={loadMore}>Load More</button>
      )}
    </div>
  );
}
```

### Vue.js Plugin

```javascript
import { createApp } from 'vue';
import PermaSearchPlugin from '@permasearch/vue';

const app = createApp(App);
app.use(PermaSearchPlugin, {
  apiKey: 'your-api-key'
});

// In component
export default {
  data() {
    return {
      query: '',
      results: [],
      loading: false
    };
  },
  methods: {
    async search() {
      this.loading = true;
      try {
        this.results = await this.$permasearch.search(this.query);
      } finally {
        this.loading = false;
      }
    }
  }
};
```

### Next.js Integration

```javascript
// pages/api/search.js
import { PermaSearch } from '@permasearch/sdk';

const client = new PermaSearch({
  apiKey: process.env.PERMASEARCH_API_KEY
});

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  try {
    const results = await client.search(req.body);
    res.status(200).json(results);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
}
```

## 🧪 Testing

### Unit Testing

```javascript
import { PermaSearch } from '@permasearch/sdk';

// Mock the SDK for testing
jest.mock('@permasearch/sdk');

describe('SearchService', () => {
  let mockClient;

  beforeEach(() => {
    mockClient = {
      search: jest.fn(),
      connectWallet: jest.fn()
    };
    PermaSearch.mockImplementation(() => mockClient);
  });

  it('should search successfully', async () => {
    mockClient.search.mockResolvedValue({
      results: [{ id: '1', title: 'Test' }],
      total: 1
    });

    const results = await searchService.search('test');
    expect(results.total).toBe(1);
  });
});
```

### Integration Testing

```javascript
// Test against staging environment
const client = new PermaSearch({
  baseUrl: 'https://staging.permasearch.io/api',
  timeout: 5000
});

describe('PermaSearch Integration', () => {
  it('should handle real search requests', async () => {
    const results = await client.search('music');
    expect(results).toHaveProperty('results');
    expect(Array.isArray(results.results)).toBe(true);
  });

  it('should handle authentication', async () => {
    await client.connectWallet();
    expect(client.isAuthenticated()).toBe(true);
  });
});
```

## 📊 Monitoring & Debugging

### Request Logging

```javascript
const client = new PermaSearch({
  debug: true,
  logLevel: 'debug'
});

// All requests will be logged
const results = await client.search('debug query');
```

### Performance Monitoring

```javascript
// Track search performance
const startTime = Date.now();
const results = await client.search('performance test');
const duration = Date.now() - startTime;

console.log(`Search took ${duration}ms`);
console.log(`Found ${results.total} results`);
```

### Error Tracking

```javascript
// Implement error tracking
const client = new PermaSearch({
  onError: (error) => {
    // Send to error tracking service
    errorTracker.captureException(error);
  },
  onRequest: (request) => {
    // Log all requests
    analytics.track('api_request', {
      endpoint: request.url,
      method: request.method
    });
  }
});
```

## 📚 Code Examples

### Complete Search Application

```javascript
import { PermaSearch } from '@permasearch/sdk';

class SearchApp {
  constructor() {
    this.client = new PermaSearch({
      baseUrl: 'https://ar://permasearch/v1',
      timeout: 10000
    });
  }

  async initialize() {
    await this.client.connectWallet();
    console.log('Wallet connected:', this.client.getWalletAddress());
  }

  async searchMusic(query) {
    try {
      const results = await this.client.search({
        query,
        assetType: 'audio',
        limit: 20,
        sortBy: 'relevance'
      });

      return results.results.map(track => ({
        id: track.id,
        title: track.title,
        artist: track.creator,
        duration: track.metadata?.duration,
        url: track.contentUrl
      }));
    } catch (error) {
      console.error('Music search failed:', error);
      throw error;
    }
  }

  async getTrendingCollections() {
    const collections = await this.client.nft.getCollections({
      sortBy: 'popularity',
      limit: 10
    });

    return collections.map(collection => ({
      id: collection.id,
      name: collection.name,
      items: collection.totalItems,
      floorPrice: collection.floorPrice
    }));
  }
}

// Usage
const app = new SearchApp();
await app.initialize();

const tracks = await app.searchMusic('electronic');
const collections = await app.getTrendingCollections();
```

## 🔧 SDK Configuration Reference

### Complete Configuration Options

```typescript
interface PermaSearchConfig {
  // Network
  baseUrl?: string;
  timeout?: number;
  retries?: number;

  // Authentication
  apiKey?: string;

  // Caching
  cache?: {
    enabled?: boolean;
    ttl?: number;
    maxSize?: number;
  };

  // Rate Limiting
  rateLimit?: {
    requestsPerMinute?: number;
    burstSize?: number;
  };

  // Retry Logic
  retry?: {
    attempts?: number;
    delay?: number;
    backoff?: 'fixed' | 'exponential';
  };

  // Debugging
  debug?: boolean;
  logLevel?: 'debug' | 'info' | 'warn' | 'error';

  // Callbacks
  onError?: (error: Error) => void;
  onRequest?: (request: Request) => void;
  onResponse?: (response: Response) => void;
}
```

---

## 📞 Support & Resources

**SDK Support:**
- 📖 [API Reference](api-reference.md)
- 🐙 [GitHub Repository](https://github.com/thionne/parmawebsearch)
- 💬 [Discord Community](https://discord.gg/permasearch)
- 📧 [Developer Support](mailto:dev-support@permasearch.io)

**Latest Version:** `v1.0.0`

**License:** MIT

---

**🚀 Ready to integrate PermaSearch into your application?** Check out our [integration guide](integration.md) for more advanced use cases!
