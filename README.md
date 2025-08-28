# 🔍 PermaSearch

**Decentralized Search Engine for the Permaweb**

[![AO Protocol](https://img.shields.io/badge/Built%20on-AO%20Protocol-orange?style=flat-square&logo=arweave)](https://ao.arweave.dev)
[![Arweave](https://img.shields.io/badge/Powered%20by-Arweave-blue?style=flat-square)](https://arweave.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=flat-square&logo=typescript)](https://typescriptlang.org)
[![React](https://img.shields.io/badge/React-61DAFB?style=flat-square&logo=react)](https://reactjs.org)

## 📖 Overview

PermaSearch is an enterprise-grade decentralized search engine built on Arweave's AO Protocol. It provides censorship-resistant, permanent search capabilities across the entire Permaweb ecosystem, enabling users to discover and access immutable content through a sophisticated AI-powered interface.

### ✨ Key Capabilities

- **🔍 Comprehensive Content Discovery**: Search across images, videos, documents, audio files, and websites
- **🤖 Intelligent Agent Network**: AO-powered agents that continuously index and categorize content
- **⚡ High-Performance Search**: Progressive loading with instant cached results
- **🔐 Decentralized Authentication**: Wallet-based access without centralized accounts
- **📊 Real-Time Analytics**: Comprehensive usage tracking and performance metrics
- **🚀 Autonomous Operation**: 24/7 continuous operation with intelligent scheduling
- **💎 Permanent Storage**: All search data stored immutably on Arweave blockchain

## 🏗️ System Architecture

### Core Infrastructure

PermaSearch operates on a sophisticated multi-layer architecture designed for scalability, reliability, and decentralization.

#### AO Agent Network
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   User Interface│────│ Agent Manager   │────│   Data Cache    │
│   (React + TS)  │    │ (AO Process)    │    │  (AO Process)   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │   Arweave       │
                    │   Blockchain    │
                    │   (Permanent)   │
                    └─────────────────┘
```

#### Component Overview

- **Frontend Layer**: Modern React application with TypeScript and ShadCN UI components
- **Agent Manager**: Primary AO process orchestrating search operations and agent lifecycle
- **Data Cache**: Specialized AO process for content caching and API response optimization
- **Arweave Integration**: Direct blockchain interaction for permanent data storage and retrieval

## 🚀 Getting Started

### System Requirements

- **Node.js**: Version 18.0 or higher
- **npm**: Latest stable version
- **Arweave Wallet**: Compatible wallet (ArConnect recommended)
- **AO CLI Tools**: For process deployment
- **Git**: For repository management

### Installation & Setup

#### 1. Clone Repository
```bash
git clone <repository-url>
cd permasearch
```

#### 2. Install Dependencies
```bash
npm install
```

#### 3. Configure Environment
```bash
# Create environment configuration
cp .env.example .env

# Edit .env with your settings
nano .env
```

#### 4. Start Development Environment
```bash
npm run dev
```

### AO Network Deployment

#### Deploy Agent Processes
```bash
# Deploy Data Cache Process
node scripts/deploy-data-cache.js

# Deploy Agent Manager Process
node scripts/deploy-agent-manager.js

# Verify deployment
node scripts/verify-deployment.js
```

#### Enable Autonomous Operation
```bash
# Configure cron scheduling for 24/7 operation
aos [DATA_CACHE_PROCESS_ID] --cron 10-minutes
aos [AGENT_MANAGER_PROCESS_ID] --cron 5-minutes
```

## 📡 API Reference

PermaSearch provides a comprehensive REST API for programmatic access to search functionality and agent interactions.

### Authentication

All API requests require wallet-based authentication:

```bash
# Include wallet signature in Authorization header
-H "Authorization: Bearer <wallet-signature>"
```

### Core Endpoints

#### Search Operations

**POST** `/v1/search`
Primary search endpoint with advanced filtering capabilities.

```bash
curl -X POST "ar://permasearch/v1/search" \\
  -H "Authorization: Bearer <wallet-signature>" \\
  -H "Content-Type: application/json" \\
  -d '{
    "query": "NFT collection",
    "assetType": "image",
    "limit": 20,
    "offset": 0
  }'
```

**Parameters:**
- `query` (string): Search terms
- `assetType` (string): Content type filter (image, audio, video, document)
- `limit` (number): Results per page (max 100)
- `offset` (number): Pagination offset

**Response:**
```json
{
  "results": [
    {
      "id": "transaction_id",
      "name": "Content Title",
      "assetType": "image",
      "creator": "wallet_address",
      "data": "https://arweave.net/transaction_id",
      "metadata": { ... }
    }
  ],
  "total": 150,
  "hasMore": true
}
```

#### NFT Collection Access

**GET** `/v1/nft/collection/{collectionId}`
Retrieve detailed information about specific NFT collections.

```bash
curl -X GET "ar://permasearch/v1/nft/collection/ArweavePunks" \\
  -H "Authorization: Bearer <wallet-signature>"
```

#### Agent Interactions

**POST** `/v1/agents/query`
Direct interaction with autonomous agents.

```bash
curl -X POST "ar://permasearch/v1/agents/query" \\
  -H "Authorization: Bearer <wallet-signature>" \\
  -H "Content-Type: application/json" \\
  -d '{
    "agentId": "content_discovery_agent",
    "message": "Find electronic music tracks",
    "sessionId": "optional_session_id"
  }'
```

## ⚙️ Configuration

### Environment Configuration

Create a `.env` file in the project root with the following variables:

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
```

### Wallet Integration

PermaSearch integrates seamlessly with Arweave-compatible wallets:

```typescript
// Initialize AO connection
import { connect } from '@permaweb/aoconnect';

const ao = connect();

// Wallet connection is handled automatically by the application
// Users can connect via ArConnect, Arweave.app, or other compatible wallets
```

### Process Configuration

After deploying AO processes, update your environment with the generated process IDs:

```bash
# Example process IDs (replace with your actual deployed processes)
export VITE_AGENT_MANAGER_PROCESS_ID="A1B2C3D4E5F6..."
export VITE_DATA_CACHE_PROCESS_ID="F6E5D4C3B2A1..."
```

## 🤖 Autonomous Agent System

PermaSearch operates continuously through intelligent AO agents that maintain system health and data freshness.

### Agent Manager Process

**Scheduling**: Every 5 minutes
**Responsibilities**:
- Orchestrates agent lifecycle management
- Monitors system performance metrics
- Delegates search requests to appropriate agents
- Maintains operational logs and audit trails

### Data Cache Process

**Scheduling**: Every 10 minutes
**Responsibilities**:
- Refreshes cached content from Arweave
- Discovers new blockchain transactions
- Updates search indexes automatically
- Maintains data consistency and freshness

### System Monitoring

Monitor agent health and performance through the AO CLI:

```bash
# Monitor specific process
aos [AGENT_MANAGER_PROCESS_ID] --monitor

# Check agent health status
node scripts/health-check.js

# View system metrics
node scripts/system-metrics.js
```

### Process Health Indicators

- **Uptime**: 99.9% target availability
- **Response Time**: <500ms average query response
- **Cache Hit Rate**: >85% for frequently accessed content
- **Error Rate**: <0.1% system error rate

## 📂 Project Architecture

```
permasearch/
├── src/
│   ├── components/              # React UI Components
│   │   ├── Hero.tsx            # Landing page hero section
│   │   ├── Features.tsx        # Product features showcase
│   │   ├── ApiShowcase.tsx     # API documentation interface
│   │   ├── SearchResults.tsx   # Search results display
│   │   ├── Footer.tsx          # Site footer with navigation
│   │   └── ui/                 # ShadCN UI component library
│   ├── pages/                  # Route-based page components
│   │   ├── Index.tsx           # Homepage
│   │   ├── Search.tsx          # Main search interface
│   │   ├── ApiDocs.tsx         # API documentation
│   │   ├── SearchConsole.tsx   # Developer testing console
│   │   └── WalletIntegration.tsx # Wallet connection guide
│   ├── services/               # Business logic and API integrations
│   │   ├── aoAgentService.ts   # AO agent communication layer
│   │   ├── permawebSearch.ts   # Core search functionality
│   │   ├── arweave/            # Arweave blockchain integration
│   │   ├── integration/        # Service orchestration
│   │   └── userApiKeyService.ts # API key management
│   └── hooks/                  # Custom React hooks
│       ├── usePermawebSearch.ts # Search state management
│       ├── useWallet.ts        # Wallet connection handling
│       └── useUserAccount.ts   # User account management
├── ao-processes/               # AO Protocol Lua Processes
│   ├── agent-manager.lua       # Primary agent orchestration
│   └── data-cache.lua          # Content caching and indexing
├── scripts/                    # Deployment and utility scripts
│   ├── deploy-data-cache.js    # Data cache deployment
│   ├── deploy-agent-manager.js # Agent manager deployment
│   └── verify-deployment.js    # Deployment verification
├── public/                     # Static assets
│   ├── favicon.ico
│   ├── manifest.json
│   └── robots.txt
└── docs/                       # Extended documentation
    ├── api-reference.md
    ├── deployment-guide.md
    └── architecture-overview.md
```

## 🎯 Target Applications

### Content Discovery Platforms
- **Digital Asset Marketplaces**: NFT collection browsing and discovery
- **Media Libraries**: Permanent audio, video, and document archives
- **Research Repositories**: Academic and scientific content access
- **Creative Portfolios**: Artist and creator content showcases

### Developer Tools
- **API Integration Services**: Backend search functionality for applications
- **Blockchain Explorers**: Enhanced transaction and content browsing
- **Data Analytics Platforms**: Arweave data mining and analysis tools
- **Research Applications**: Academic and scientific data discovery

### Enterprise Solutions
- **Compliance Archiving**: Regulatory-compliant permanent data storage
- **Audit Systems**: Immutable transaction and activity logging
- **Content Management**: Decentralized digital asset management
- **Analytics Platforms**: Blockchain data intelligence and insights

## 🔒 Security Architecture

### Decentralized Authentication
- **Wallet-Based Access**: Arweave-compatible wallet integration
- **Zero-Trust Model**: No centralized user accounts or credentials
- **Cryptographic Verification**: All requests cryptographically signed

### Data Protection
- **End-to-End Encryption**: Secure communication channels
- **Immutable Storage**: Arweave's permanent, tamper-proof storage
- **Censorship Resistance**: No single point of failure or control
- **Audit Trails**: Complete transaction history and access logs

## ⚡ Performance Characteristics

### Query Optimization
- **Progressive Loading**: Instant cached results with streaming updates
- **Intelligent Caching**: Multi-level caching strategy for optimal performance
- **Parallel Processing**: Concurrent agent queries for faster results
- **Fuzzy Matching**: Smart search algorithms for relevant results

### System Metrics
- **Response Time**: <500ms average query response time
- **Cache Hit Rate**: >85% for frequently accessed content
- **Uptime**: 99.9% service availability target
- **Scalability**: Horizontal scaling through AO agent network

## 🤝 Contributing

We welcome contributions from the community! PermaSearch is an open-source project built on decentralized principles.

### How to Contribute

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

### Development Workflow

```bash
# Clone the repository
git clone https://github.com/permasearch/permasearch.git
cd permasearch

# Install dependencies
npm install

# Start development server
npm run dev

# Run linting
npm run lint

# Build for production
npm run build
```

### Contribution Guidelines

- Follow TypeScript best practices
- Add tests for new features
- Update documentation for API changes
- Ensure all tests pass before submitting PR
- Use conventional commit messages

## 📖 Documentation

### Getting Started
- **[Installation Guide](docs/installation.md)** - Complete setup instructions
- **[Quick Start](docs/quick-start.md)** - 5-minute setup guide
- **[Configuration](docs/configuration.md)** - Environment and deployment config

### API Documentation
- **[API Reference](docs/api-reference.md)** - Complete API specification
- **[SDK Documentation](docs/sdk.md)** - Client library usage
- **[Integration Guide](docs/integration.md)** - Third-party integrations

### Technical Documentation
- **[Architecture Overview](docs/architecture.md)** - System design and components
- **[Deployment Guide](docs/deployment.md)** - AO process deployment
- **[Monitoring Guide](docs/monitoring.md)** - System monitoring and maintenance

## 📞 Support & Community

### Community Resources
- **GitHub Issues**: [Report bugs and request features](https://github.com/permasearch/permasearch/issues)
- **Discussions**: [Community forum](https://github.com/permasearch/permasearch/discussions)
- **Discord**: [Real-time community chat](https://discord.gg/permasearch)

### Professional Support
- **Enterprise Support**: Contact us for commercial licensing and support
- **Integration Services**: Custom integration and deployment services
- **Training**: Developer training and workshops

## 📜 License

PermaSearch is licensed under the **MIT License**. See [LICENSE](LICENSE) for full terms.

The MIT License allows for:
- ✅ Commercial use
- ✅ Private use
- ✅ Modification
- ✅ Distribution
- ⚠️ No warranty or liability

## 🙏 Acknowledgments

### Technology Partners
- **AO Protocol Team** - Revolutionary autonomous computing platform
- **Arweave Foundation** - Permanent, decentralized storage infrastructure
- **AO Community** - Incredible ecosystem and developer support

### Open Source Libraries
- **React & TypeScript** - Modern web application framework
- **ShadCN/UI** - Beautiful, accessible component library
- **AO Connect** - AO protocol integration library
- **Arweave.js** - Arweave blockchain integration

### Contributors
We extend our gratitude to all contributors who help make PermaSearch better through their code, documentation, and community engagement.

---

## 🌟 About PermaSearch

PermaSearch represents the future of decentralized search - a censorship-resistant, permanent search engine that operates autonomously on the Arweave blockchain through AO Protocol agents. Built for the decentralized web, it empowers users and developers to discover and access immutable content without intermediaries.

**Built with ❤️ for the decentralized future**

### Connect With Us
- 🌐 **Website**: [permasearch.io](https://permasearch.io)
- 📚 **Documentation**: [docs.permasearch.io](https://docs.permasearch.io)
- 🐙 **GitHub**: [github.com/permasearch/permasearch](https://github.com/permasearch/permasearch)
- 🐦 **X (Twitter)**: [@PermaSearch_AO](https://x.com/PermaSearch_AO)
# parmawebsearch
