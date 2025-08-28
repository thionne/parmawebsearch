# 🔗 Integration Guide

## Third-Party Integrations for PermaSearch

Comprehensive guide for integrating PermaSearch into various platforms, frameworks, and applications.

## 🌐 Web Integration

### HTML Widget Integration

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Website with PermaSearch</title>
    <script src="https://cdn.permasearch.io/widget/v1/permasearch-widget.js"></script>
    <link rel="stylesheet" href="https://cdn.permasearch.io/widget/v1/permasearch-widget.css">
</head>
<body>
    <h1>My Website</h1>

    <!-- PermaSearch Widget -->
    <div id="permasearch-widget"></div>

    <script>
        // Initialize widget
        PermaSearchWidget.init({
            container: '#permasearch-widget',
            theme: 'light',
            placeholder: 'Search permanent content...',
            assetTypes: ['image', 'audio', 'video', 'document'],
            maxResults: 10
        });

        // Handle search events
        PermaSearchWidget.on('search', (query, results) => {
            console.log('Search performed:', query, results);
        });

        PermaSearchWidget.on('result-click', (result) => {
            console.log('Result clicked:', result);
        });
    </script>
</body>
</html>
```

### WordPress Plugin

```php
<?php
/**
 * Plugin Name: PermaSearch for WordPress
 * Description: Integrate PermaSearch into WordPress
 * Version: 1.0.0
 */

// Enqueue scripts and styles
function permasearch_enqueue_scripts() {
    wp_enqueue_script(
        'permasearch-sdk',
        'https://cdn.permasearch.io/sdk/v1/permasearch.min.js',
        array(),
        '1.0.0',
        true
    );

    wp_enqueue_style(
        'permasearch-styles',
        'https://cdn.permasearch.io/widget/v1/permasearch-widget.css',
        array(),
        '1.0.0'
    );
}
add_action('wp_enqueue_scripts', 'permasearch_enqueue_scripts');

// Add search widget
function permasearch_search_widget() {
    ?>
    <div id="permasearch-search-widget">
        <input type="text" id="permasearch-query" placeholder="Search Arweave...">
        <div id="permasearch-results"></div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const client = new PermaSearch();
            const queryInput = document.getElementById('permasearch-query');
            const resultsDiv = document.getElementById('permasearch-results');

            queryInput.addEventListener('input', async function() {
                const query = this.value.trim();
                if (query.length < 2) return;

                try {
                    const results = await client.search(query);
                    displayResults(results.results);
                } catch (error) {
                    console.error('Search error:', error);
                }
            });

            function displayResults(results) {
                resultsDiv.innerHTML = results.map(result => `
                    <div class="permasearch-result">
                        <h4>${result.title}</h4>
                        <p>By: ${result.creator}</p>
                        <a href="${result.contentUrl}" target="_blank">View Content</a>
                    </div>
                `).join('');
            }
        });
    </script>
    <?php
}
add_shortcode('permasearch', 'permasearch_search_widget');
```

### Shopify App

```liquid
<!-- themes/your-theme/templates/search.liquid -->
<div class="permasearch-integration">
    <div class="search-tabs">
        <button class="tab active" data-type="shopify">Shop Products</button>
        <button class="tab" data-type="permasearch">Arweave Content</button>
    </div>

    <div class="search-container">
        <input type="text" id="unified-search" placeholder="Search products and permanent content...">
        <div id="search-results"></div>
    </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
    const searchInput = document.getElementById('unified-search');
    const resultsDiv = document.getElementById('search-results');
    const tabs = document.querySelectorAll('.tab');

    let currentSearchType = 'shopify';

    // Tab switching
    tabs.forEach(tab => {
        tab.addEventListener('click', function() {
            tabs.forEach(t => t.classList.remove('active'));
            this.classList.add('active');
            currentSearchType = this.dataset.type;
            performSearch();
        });
    });

    // Search functionality
    searchInput.addEventListener('input', debounce(performSearch, 300));

    async function performSearch() {
        const query = searchInput.value.trim();
        if (!query) return;

        try {
            if (currentSearchType === 'shopify') {
                // Standard Shopify search
                const shopifyResults = await searchShopify(query);
                displayShopifyResults(shopifyResults);
            } else {
                // PermaSearch integration
                const client = new PermaSearch();
                const results = await client.search(query);
                displayPermaSearchResults(results.results);
            }
        } catch (error) {
            console.error('Search error:', error);
        }
    }

    function displayShopifyResults(products) {
        resultsDiv.innerHTML = products.map(product => `
            <div class="product-result">
                <img src="${product.image}" alt="${product.title}">
                <h3>${product.title}</h3>
                <p>${product.price}</p>
                <a href="/products/${product.handle}">View Product</a>
            </div>
        `).join('');
    }

    function displayPermaSearchResults(results) {
        resultsDiv.innerHTML = results.map(result => `
            <div class="permasearch-result">
                <h3>${result.title}</h3>
                <p>Creator: ${result.creator}</p>
                <p>Type: ${result.assetType}</p>
                <a href="${result.contentUrl}" target="_blank">View on Arweave</a>
            </div>
        `).join('');
    }

    function debounce(func, wait) {
        let timeout;
        return function executedFunction(...args) {
            const later = () => {
                clearTimeout(timeout);
                func(...args);
            };
            clearTimeout(timeout);
            timeout = setTimeout(later, wait);
        };
    }
});
</script>
```

## 📱 Mobile App Integration

### React Native Integration

```javascript
// App.js
import React, { useState } from 'react';
import { View, TextInput, FlatList, TouchableOpacity, Text } from 'react-native';
import { PermaSearch } from '@permasearch/react-native';

export default function App() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  const handleSearch = async () => {
    if (!query.trim()) return;

    setLoading(true);
    try {
      const client = new PermaSearch();
      const searchResults = await client.search({
        query: query,
        assetType: 'image',
        limit: 20
      });
      setResults(searchResults.results);
    } catch (error) {
      console.error('Search error:', error);
    } finally {
      setLoading(false);
    }
  };

  const renderResult = ({ item }) => (
    <TouchableOpacity
      style={styles.resultItem}
      onPress={() => Linking.openURL(item.contentUrl)}
    >
      <Text style={styles.title}>{item.title}</Text>
      <Text style={styles.creator}>By: {item.creator}</Text>
      <Text style={styles.type}>{item.assetType}</Text>
    </TouchableOpacity>
  );

  return (
    <View style={styles.container}>
      <TextInput
        style={styles.searchInput}
        placeholder="Search permanent content..."
        value={query}
        onChangeText={setQuery}
        onSubmitEditing={handleSearch}
      />

      {loading && <Text>Loading...</Text>}

      <FlatList
        data={results}
        renderItem={renderResult}
        keyExtractor={(item) => item.id}
        style={styles.resultsList}
      />
    </View>
  );
}

const styles = {
  container: {
    flex: 1,
    padding: 20,
    backgroundColor: '#fff',
  },
  searchInput: {
    height: 40,
    borderColor: '#ccc',
    borderWidth: 1,
    borderRadius: 5,
    paddingHorizontal: 10,
    marginBottom: 20,
  },
  resultsList: {
    flex: 1,
  },
  resultItem: {
    padding: 15,
    borderBottomWidth: 1,
    borderBottomColor: '#eee',
  },
  title: {
    fontSize: 16,
    fontWeight: 'bold',
    marginBottom: 5,
  },
  creator: {
    fontSize: 14,
    color: '#666',
    marginBottom: 2,
  },
  type: {
    fontSize: 12,
    color: '#999',
  },
};
```

### Flutter Integration

```dart
// lib/main.dart
import 'package:flutter/material.dart';
import 'package:permasearch_sdk/permasearch_sdk.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  final TextEditingController _controller = TextEditingController();
  final PermaSearchClient _client = PermaSearchClient();
  List<SearchResult> _results = [];
  bool _loading = false;

  Future<void> _search() async {
    final query = _controller.text.trim();
    if (query.isEmpty) return;

    setState(() => _loading = true);

    try {
      final results = await _client.search(
        query: query,
        assetType: 'image',
        limit: 20,
      );
      setState(() => _results = results.results);
    } catch (e) {
      print('Search error: $e');
    } finally {
      setState(() => _loading = false);
    }
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('PermaSearch Demo'),
        ),
        body: Column(
          children: [
            Padding(
              padding: EdgeInsets.all(16.0),
              child: TextField(
                controller: _controller,
                decoration: InputDecoration(
                  hintText: 'Search permanent content...',
                  suffixIcon: IconButton(
                    icon: Icon(Icons.search),
                    onPressed: _search,
                  ),
                ),
                onSubmitted: (_) => _search(),
              ),
            ),
            if (_loading) CircularProgressIndicator(),
            Expanded(
              child: ListView.builder(
                itemCount: _results.length,
                itemBuilder: (context, index) {
                  final result = _results[index];
                  return ListTile(
                    title: Text(result.title),
                    subtitle: Text('By: ${result.creator}'),
                    trailing: Text(result.assetType),
                    onTap: () {
                      // Open content URL
                    },
                  );
                },
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

## 🖥️ Desktop Application Integration

### Electron Integration

```javascript
// main.js
const { app, BrowserWindow, ipcMain } = require('electron');
const { PermaSearch } = require('@permasearch/sdk');

let mainWindow;
let permasearchClient;

function createWindow() {
  mainWindow = new BrowserWindow({
    width: 1200,
    height: 800,
    webPreferences: {
      nodeIntegration: true,
      contextIsolation: false
    }
  });

  mainWindow.loadFile('index.html');

  // Initialize PermaSearch
  permasearchClient = new PermaSearch({
    baseUrl: 'https://ar://permasearch/v1'
  });
}

app.whenReady().then(createWindow);

// IPC handlers for search
ipcMain.handle('search', async (event, query) => {
  try {
    const results = await permasearchClient.search(query);
    return results;
  } catch (error) {
    throw new Error(`Search failed: ${error.message}`);
  }
});

ipcMain.handle('get-content', async (event, contentId) => {
  try {
    const content = await permasearchClient.getContent(contentId);
    return content;
  } catch (error) {
    throw new Error(`Content retrieval failed: ${error.message}`);
  }
});
```

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>PermaSearch Desktop</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        #searchInput { width: 100%; padding: 10px; font-size: 16px; }
        #results { margin-top: 20px; }
        .result { border: 1px solid #ccc; padding: 10px; margin: 10px 0; }
    </style>
</head>
<body>
    <h1>PermaSearch Desktop</h1>
    <input type="text" id="searchInput" placeholder="Search permanent content...">
    <div id="results"></div>

    <script>
        const { ipcRenderer } = require('electron');

        const searchInput = document.getElementById('searchInput');
        const resultsDiv = document.getElementById('results');

        searchInput.addEventListener('input', debounce(async () => {
            const query = searchInput.value.trim();
            if (query.length < 2) return;

            try {
                const response = await ipcRenderer.invoke('search', query);
                displayResults(response.results);
            } catch (error) {
                console.error('Search error:', error);
                resultsDiv.innerHTML = '<p>Error: ' + error.message + '</p>';
            }
        }, 300));

        function displayResults(results) {
            resultsDiv.innerHTML = results.map(result => `
                <div class="result">
                    <h3>${result.title}</h3>
                    <p>Creator: ${result.creator}</p>
                    <p>Type: ${result.assetType}</p>
                    <button onclick="viewContent('${result.id}')">View Content</button>
                </div>
            `).join('');
        }

        async function viewContent(contentId) {
            try {
                const content = await ipcRenderer.invoke('get-content', contentId);
                // Open content in default application
                require('electron').shell.openExternal(content.contentUrl);
            } catch (error) {
                console.error('Content view error:', error);
            }
        }

        function debounce(func, wait) {
            let timeout;
            return function executedFunction(...args) {
                const later = () => {
                    clearTimeout(timeout);
                    func(...args);
                };
                clearTimeout(timeout);
                timeout = setTimeout(later, wait);
            };
        }
    </script>
</body>
</html>
```

## 🛠️ API Integrations

### Discord Bot Integration

```javascript
// discord-bot.js
const { Client, GatewayIntentBits } = require('discord.js');
const { PermaSearch } = require('@permasearch/sdk');

const client = new Client({
  intents: [GatewayIntentBits.Guilds, GatewayIntentBits.GuildMessages, GatewayIntentBits.MessageContent]
});

const permasearch = new PermaSearch();

client.on('ready', () => {
  console.log(`Logged in as ${client.user.tag}!`);
});

client.on('messageCreate', async message => {
  if (message.author.bot) return;

  // Check if message starts with !search
  if (message.content.startsWith('!search')) {
    const query = message.content.slice(8).trim();

    if (!query) {
      return message.reply('Please provide a search query. Usage: `!search <query>`');
    }

    try {
      await message.channel.sendTyping();

      const results = await permasearch.search({
        query: query,
        limit: 5
      });

      if (results.results.length === 0) {
        return message.reply('No results found for your search.');
      }

      const embed = {
        title: `🔍 Search Results for "${query}"`,
        description: `Found ${results.total} results`,
        color: 0x00ff88,
        fields: results.results.map((result, index) => ({
          name: `${index + 1}. ${result.title}`,
          value: `Creator: ${result.creator}\nType: ${result.assetType}\n[View Content](${result.contentUrl})`,
          inline: false
        })),
        footer: {
          text: 'Powered by PermaSearch'
        }
      };

      await message.reply({ embeds: [embed] });
    } catch (error) {
      console.error('Search error:', error);
      await message.reply('Sorry, there was an error processing your search.');
    }
  }
});

client.login(process.env.DISCORD_TOKEN);
```

### Slack App Integration

```javascript
// slack-app.js
const { App } = require('@slack/bolt');
const { PermaSearch } = require('@permasearch/sdk');

const app = new App({
  token: process.env.SLACK_BOT_TOKEN,
  signingSecret: process.env.SLACK_SIGNING_SECRET
});

const permasearch = new PermaSearch();

// Slash command for search
app.command('/permasearch', async ({ command, ack, respond }) => {
  await ack();

  const query = command.text.trim();

  if (!query) {
    return respond({
      text: 'Please provide a search query. Usage: `/permasearch <query>`',
      response_type: 'ephemeral'
    });
  }

  try {
    const results = await permasearch.search({
      query: query,
      limit: 5
    });

    if (results.results.length === 0) {
      return respond({
        text: `No results found for "${query}"`,
        response_type: 'ephemeral'
      });
    }

    const blocks = [
      {
        type: 'header',
        text: {
          type: 'plain_text',
          text: `🔍 Search Results for "${query}"`
        }
      },
      {
        type: 'section',
        text: {
          type: 'mrkdwn',
          text: `Found ${results.total} results on the Permaweb`
        }
      }
    ];

    results.results.forEach((result, index) => {
      blocks.push({
        type: 'section',
        text: {
          type: 'mrkdwn',
          text: `*${index + 1}. ${result.title}*\nCreator: ${result.creator}\nType: ${result.assetType}`
        },
        accessory: {
          type: 'button',
          text: {
            type: 'plain_text',
            text: 'View'
          },
          url: result.contentUrl
        }
      });
    });

    await respond({
      blocks: blocks,
      response_type: 'in_channel'
    });
  } catch (error) {
    console.error('Search error:', error);
    await respond({
      text: 'Sorry, there was an error processing your search.',
      response_type: 'ephemeral'
    });
  }
});

(async () => {
  await app.start(process.env.PORT || 3000);
  console.log('PermaSearch Slack app is running!');
})();
```

### Telegram Bot Integration

```javascript
// telegram-bot.js
const TelegramBot = require('node-telegram-bot-api');
const { PermaSearch } = require('@permasearch/sdk');

const bot = new TelegramBot(process.env.TELEGRAM_TOKEN, { polling: true });
const permasearch = new PermaSearch();

// Handle /search command
bot.onText(/\/search (.+)/, async (msg, match) => {
  const chatId = msg.chat.id;
  const query = match[1].trim();

  if (!query) {
    return bot.sendMessage(chatId, 'Please provide a search query. Usage: `/search <query>`', {
      parse_mode: 'Markdown'
    });
  }

  try {
    await bot.sendChatAction(chatId, 'typing');

    const results = await permasearch.search({
      query: query,
      limit: 5
    });

    if (results.results.length === 0) {
      return bot.sendMessage(chatId, `No results found for "${query}"`);
    }

    let message = `🔍 *Search Results for "${query}"*\nFound ${results.total} results\n\n`;

    results.results.forEach((result, index) => {
      message += `${index + 1}. *${result.title}*\n`;
      message += `   Creator: ${result.creator}\n`;
      message += `   Type: ${result.assetType}\n`;
      message += `   [View Content](${result.contentUrl})\n\n`;
    });

    await bot.sendMessage(chatId, message, {
      parse_mode: 'Markdown',
      disable_web_page_preview: true
    });
  } catch (error) {
    console.error('Search error:', error);
    await bot.sendMessage(chatId, 'Sorry, there was an error processing your search.');
  }
});

// Handle inline queries
bot.on('inline_query', async (query) => {
  const searchQuery = query.query.trim();

  if (!searchQuery) return;

  try {
    const results = await permasearch.search({
      query: searchQuery,
      limit: 10
    });

    const inlineResults = results.results.map((result, index) => ({
      type: 'article',
      id: result.id,
      title: result.title,
      description: `By ${result.creator} (${result.assetType})`,
      input_message_content: {
        message_text: `${result.title}\nBy: ${result.creator}\nType: ${result.assetType}\n\n[View Content](${result.contentUrl})`
      },
      url: result.contentUrl,
      hide_url: false
    }));

    await bot.answerInlineQuery(query.id, inlineResults, {
      cache_time: 300 // 5 minutes
    });
  } catch (error) {
    console.error('Inline query error:', error);
  }
});

// Welcome message
bot.onText(/\/start/, (msg) => {
  const chatId = msg.chat.id;
  const welcomeMessage = `
🤖 *Welcome to PermaSearch Bot!*

I can help you search for permanent content on the Arweave network.

*Commands:*
• /search <query> - Search for content
• /help - Show this help message

*Inline Search:*
Type @YourBotName followed by your search query in any chat!

_Powered by PermaSearch on AO Protocol_
  `;

  bot.sendMessage(chatId, welcomeMessage, {
    parse_mode: 'Markdown'
  });
});

console.log('PermaSearch Telegram bot is running!');
```

## 🔧 Custom Integration Framework

### Base Integration Class

```javascript
// integrations/BaseIntegration.js
class BasePermaSearchIntegration {
  constructor(config = {}) {
    this.config = {
      baseUrl: 'https://ar://permasearch/v1',
      timeout: 10000,
      retries: 3,
      ...config
    };

    this.client = new PermaSearch(this.config);
  }

  async initialize() {
    // Override in subclasses
    throw new Error('initialize() must be implemented by subclass');
  }

  async search(query, options = {}) {
    try {
      const results = await this.client.search({
        query,
        ...options
      });

      return this.transformResults(results);
    } catch (error) {
      return this.handleError(error);
    }
  }

  transformResults(results) {
    // Override in subclasses to customize result format
    return results;
  }

  handleError(error) {
    // Override in subclasses for custom error handling
    console.error('PermaSearch error:', error);
    throw error;
  }

  async getContent(contentId) {
    return await this.client.getContent(contentId);
  }

  async getCollections(options = {}) {
    return await this.client.nft.getCollections(options);
  }
}

module.exports = BasePermaSearchIntegration;
```

### Custom Website Integration

```javascript
// integrations/CustomWebsite.js
const BasePermaSearchIntegration = require('./BaseIntegration');

class CustomWebsiteIntegration extends BasePermaSearchIntegration {
  constructor(config = {}) {
    super(config);
    this.searchContainer = null;
    this.resultsContainer = null;
  }

  async initialize() {
    // Create search interface
    this.createSearchInterface();

    // Bind event handlers
    this.bindEvents();

    console.log('Custom website integration initialized');
  }

  createSearchInterface() {
    // Create search input
    const searchInput = document.createElement('input');
    searchInput.type = 'text';
    searchInput.placeholder = this.config.placeholder || 'Search permanent content...';
    searchInput.className = 'permasearch-input';

    // Create results container
    const resultsContainer = document.createElement('div');
    resultsContainer.className = 'permasearch-results';

    // Add to DOM
    const container = document.querySelector(this.config.container || '#permasearch');
    container.appendChild(searchInput);
    container.appendChild(resultsContainer);

    this.searchInput = searchInput;
    this.resultsContainer = resultsContainer;
  }

  bindEvents() {
    let searchTimeout;

    this.searchInput.addEventListener('input', (e) => {
      clearTimeout(searchTimeout);
      const query = e.target.value.trim();

      if (query.length < 2) {
        this.clearResults();
        return;
      }

      searchTimeout = setTimeout(() => {
        this.performSearch(query);
      }, this.config.debounceDelay || 300);
    });
  }

  async performSearch(query) {
    try {
      this.showLoading();

      const results = await this.search(query, {
        assetType: this.config.assetType,
        limit: this.config.limit || 10
      });

      this.displayResults(results);
    } catch (error) {
      this.showError(error);
    } finally {
      this.hideLoading();
    }
  }

  transformResults(results) {
    return {
      ...results,
      results: results.results.map(result => ({
        ...result,
        displayTitle: this.formatTitle(result.title),
        thumbnail: this.getThumbnail(result),
        formattedDate: this.formatDate(result.createdAt)
      }))
    };
  }

  displayResults(results) {
    if (results.results.length === 0) {
      this.resultsContainer.innerHTML = '<p>No results found</p>';
      return;
    }

    const html = results.results.map(result => `
      <div class="result-item" data-id="${result.id}">
        ${result.thumbnail ? `<img src="${result.thumbnail}" alt="${result.title}" class="result-thumbnail">` : ''}
        <div class="result-content">
          <h3 class="result-title">${result.displayTitle}</h3>
          <p class="result-creator">By: ${result.creator}</p>
          <p class="result-meta">${result.assetType} • ${result.formattedDate}</p>
          <a href="${result.contentUrl}" target="_blank" class="result-link">View Content</a>
        </div>
      </div>
    `).join('');

    this.resultsContainer.innerHTML = html;

    // Add click handlers
    this.resultsContainer.querySelectorAll('.result-item').forEach(item => {
      item.addEventListener('click', (e) => {
        if (!e.target.classList.contains('result-link')) {
          const contentId = item.dataset.id;
          this.onResultClick(contentId, results.results.find(r => r.id === contentId));
        }
      });
    });
  }

  // Utility methods
  formatTitle(title) {
    return title.length > 50 ? title.substring(0, 50) + '...' : title;
  }

  getThumbnail(result) {
    return result.thumbnailUrl || result.contentUrl;
  }

  formatDate(dateString) {
    return new Date(dateString).toLocaleDateString();
  }

  showLoading() {
    this.resultsContainer.innerHTML = '<p>Searching...</p>';
  }

  hideLoading() {
    // Loading is cleared when results are displayed
  }

  showError(error) {
    this.resultsContainer.innerHTML = `<p class="error">Error: ${error.message}</p>`;
  }

  clearResults() {
    this.resultsContainer.innerHTML = '';
  }

  onResultClick(contentId, result) {
    // Override in subclasses for custom behavior
    console.log('Result clicked:', contentId, result);
  }
}

module.exports = CustomWebsiteIntegration;
```

### Usage Example

```javascript
// Initialize custom integration
const integration = new CustomWebsiteIntegration({
  container: '#search-container',
  placeholder: 'Search our permanent archive...',
  assetType: 'document',
  limit: 20,
  debounceDelay: 500
});

integration.initialize();

// Custom result click handler
integration.onResultClick = (contentId, result) => {
  // Open in modal or custom viewer
  openContentModal(result);
};
```

## 📊 Analytics Integration

### Google Analytics Integration

```javascript
// analytics-integration.js
class PermaSearchAnalytics {
  constructor(gaTrackingId) {
    this.gaTrackingId = gaTrackingId;
    this.client = new PermaSearch();
  }

  async trackSearch(query, results) {
    // Send to Google Analytics
    gtag('event', 'search', {
      search_term: query,
      results_count: results.total,
      custom_parameter_1: 'permasearch'
    });

    // Custom analytics
    this.sendToCustomAnalytics('search', {
      query,
      resultsCount: results.total,
      timestamp: new Date().toISOString()
    });
  }

  async trackResultClick(result) {
    // Track result interactions
    gtag('event', 'select_content', {
      content_type: result.assetType,
      content_id: result.id,
      search_term: this.lastQuery
    });

    this.sendToCustomAnalytics('result_click', {
      contentId: result.id,
      contentType: result.assetType,
      creator: result.creator
    });
  }

  async sendToCustomAnalytics(event, data) {
    try {
      await fetch('/api/analytics', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          event,
          data,
          timestamp: new Date().toISOString()
        })
      });
    } catch (error) {
      console.error('Analytics error:', error);
    }
  }
}

// Usage
const analytics = new PermaSearchAnalytics('GA_TRACKING_ID');

const results = await client.search('music');
await analytics.trackSearch('music', results);

// Track result clicks
document.addEventListener('click', async (e) => {
  if (e.target.classList.contains('result-link')) {
    const resultId = e.target.dataset.resultId;
    const result = results.find(r => r.id === resultId);
    if (result) {
      await analytics.trackResultClick(result);
    }
  }
});
```

---

## 🎯 Integration Checklist

### Pre-Integration
- [ ] Review [API Reference](api-reference.md)
- [ ] Check [SDK Documentation](sdk.md)
- [ ] Understand authentication requirements
- [ ] Plan integration architecture

### Implementation
- [ ] Install SDK or implement API calls
- [ ] Set up authentication
- [ ] Implement search functionality
- [ ] Add result display
- [ ] Handle errors gracefully

### Testing
- [ ] Test search functionality
- [ ] Verify result display
- [ ] Check error handling
- [ ] Test on different devices/browsers

### Deployment
- [ ] Configure production environment
- [ ] Set up monitoring
- [ ] Implement analytics
- [ ] Test in production

### Maintenance
- [ ] Monitor API usage
- [ ] Keep SDK updated
- [ ] Handle API changes
- [ ] User support

---

## 📞 Support & Resources

**Integration Support:**
- 📖 [API Reference](api-reference.md)
- 🛠️ [SDK Documentation](sdk.md)
- 💬 [Community Discord](https://discord.gg/permasearch)
- 📧 [Integration Support](mailto:integrations@permasearch.io)

**Example Projects:**
- 🔗 [GitHub Examples](https://github.com/thionne/parmawebsearch/tree/main/examples)
- 📱 [Demo Applications](https://github.com/thionne/parmawebsearch/tree/main/demos)

**Professional Services:**
- Custom integration development
- Enterprise support packages
- Training and workshops
- Migration assistance

---

**🚀 Ready to integrate PermaSearch?** Start with our [Quick Start Guide](quick-start.md) and join thousands of applications already using our decentralized search platform!
