# 📊 Monitoring Guide

## System Monitoring and Maintenance

Complete guide for monitoring PermaSearch performance, health, and maintaining optimal operation.

## 🔍 System Health Monitoring

### Health Check Endpoints

#### Application Health

```http
GET /health
```

**Response:**
```json
{
  "status": "healthy",
  "timestamp": "2024-01-15T10:30:00Z",
  "version": "1.0.0",
  "checks": {
    "ao_network": "healthy",
    "database": "healthy",
    "cache": "healthy",
    "search_engine": "healthy"
  },
  "uptime": 86400,
  "memory": {
    "used": 150000000,
    "total": 500000000,
    "percentage": 30
  }
}
```

#### AO Process Health

```javascript
// monitor-ao-health.js
const { PermaSearch } = require('@permasearch/sdk');

class AOHealthMonitor {
  constructor() {
    this.client = new PermaSearch();
    this.alerts = [];
    this.metrics = new Map();
  }

  async checkAllProcesses() {
    const processes = [
      process.env.VITE_AGENT_MANAGER_PROCESS_ID,
      process.env.VITE_DATA_CACHE_PROCESS_ID
    ];

    const results = await Promise.allSettled(
      processes.map(id => this.checkProcessHealth(id))
    );

    return results.map((result, index) => ({
      processId: processes[index],
      status: result.status === 'fulfilled' ? result.value : 'error',
      details: result.status === 'fulfilled' ? result.value : result.reason
    }));
  }

  async checkProcessHealth(processId) {
    try {
      const status = await this.client.agents.getStatus();
      const process = status.agents.find(a => a.id === processId);

      if (!process) {
        throw new Error('Process not found');
      }

      return {
        id: process.id,
        status: process.status,
        uptime: process.uptime,
        lastActive: process.lastActive,
        version: process.version,
        health: this.evaluateHealth(process)
      };
    } catch (error) {
      throw new Error(`Health check failed: ${error.message}`);
    }
  }

  evaluateHealth(process) {
    const { status, uptime } = process;

    if (status !== 'active') {
      return { level: 'critical', score: 0 };
    }

    if (uptime < 0.95) {
      return { level: 'warning', score: 70 };
    }

    return { level: 'healthy', score: 100 };
  }

  async getSystemMetrics() {
    return {
      timestamp: new Date().toISOString(),
      processes: await this.checkAllProcesses(),
      performance: await this.getPerformanceMetrics(),
      errors: await this.getErrorMetrics(),
      alerts: this.alerts
    };
  }

  async getPerformanceMetrics() {
    // Implement performance monitoring
    return {
      responseTime: await this.measureResponseTime(),
      throughput: await this.measureThroughput(),
      cacheHitRate: await this.measureCacheEfficiency(),
      memoryUsage: process.memoryUsage()
    };
  }

  async measureResponseTime() {
    const startTime = Date.now();
    await this.client.search('test');
    return Date.now() - startTime;
  }

  async measureThroughput() {
    // Measure requests per second
    const testDuration = 10000; // 10 seconds
    const startTime = Date.now();
    let requestCount = 0;

    const testRequests = async () => {
      const promises = [];
      for (let i = 0; i < 10; i++) {
        promises.push(this.client.search(`test query ${i}`));
      }
      await Promise.all(promises);
      requestCount += 10;
    };

    while (Date.now() - startTime < testDuration) {
      await testRequests();
    }

    return requestCount / (testDuration / 1000);
  }

  async measureCacheEfficiency() {
    // Implement cache hit rate measurement
    return 0.87; // Placeholder
  }
}

// Usage
const monitor = new AOHealthMonitor();

// Check health every 30 seconds
setInterval(async () => {
  const health = await monitor.getSystemMetrics();
  console.log('System Health:', health);
}, 30000);
```

## 📈 Performance Metrics

### Search Performance Monitoring

```javascript
// performance-monitor.js
class SearchPerformanceMonitor {
  constructor() {
    this.metrics = {
      totalSearches: 0,
      averageResponseTime: 0,
      cacheHitRate: 0,
      errorRate: 0,
      topQueries: new Map()
    };
  }

  recordSearch(query, responseTime, success, cached = false) {
    this.metrics.totalSearches++;

    // Update average response time
    this.metrics.averageResponseTime =
      (this.metrics.averageResponseTime * (this.metrics.totalSearches - 1) + responseTime) /
      this.metrics.totalSearches;

    // Update cache hit rate
    this.metrics.cacheHitRate = cached ?
      (this.metrics.cacheHitRate * (this.metrics.totalSearches - 1) + 1) / this.metrics.totalSearches :
      (this.metrics.cacheHitRate * (this.metrics.totalSearches - 1)) / this.metrics.totalSearches;

    // Update error rate
    if (!success) {
      this.metrics.errorRate = (this.metrics.errorRate * (this.metrics.totalSearches - 1) + 1) /
                              this.metrics.totalSearches;
    }

    // Track popular queries
    this.trackQuery(query);
  }

  trackQuery(query) {
    const count = this.metrics.topQueries.get(query) || 0;
    this.metrics.topQueries.set(query, count + 1);
  }

  getTopQueries(limit = 10) {
    return Array.from(this.metrics.topQueries.entries())
      .sort((a, b) => b[1] - a[1])
      .slice(0, limit)
      .map(([query, count]) => ({ query, count }));
  }

  getMetrics() {
    return {
      ...this.metrics,
      topQueries: this.getTopQueries(),
      timestamp: new Date().toISOString()
    };
  }

  reset() {
    this.metrics = {
      totalSearches: 0,
      averageResponseTime: 0,
      cacheHitRate: 0,
      errorRate: 0,
      topQueries: new Map()
    };
  }
}

// Integration with search service
const performanceMonitor = new SearchPerformanceMonitor();

// Example usage in search handler
app.post('/api/search', async (req, res) => {
  const startTime = Date.now();
  const { query } = req.body;

  try {
    const results = await performSearch(query);
    const responseTime = Date.now() - startTime;
    const cached = results.cached || false;

    performanceMonitor.recordSearch(query, responseTime, true, cached);

    res.json(results);
  } catch (error) {
    const responseTime = Date.now() - startTime;
    performanceMonitor.recordSearch(query, responseTime, false);

    res.status(500).json({ error: error.message });
  }
});
```

### Cache Performance Monitoring

```javascript
// cache-monitor.js
class CacheMonitor {
  constructor() {
    this.metrics = {
      hits: 0,
      misses: 0,
      sets: 0,
      deletes: 0,
      size: 0,
      hitRate: 0
    };
  }

  recordHit() {
    this.metrics.hits++;
    this.updateHitRate();
  }

  recordMiss() {
    this.metrics.misses++;
    this.updateHitRate();
  }

  recordSet() {
    this.metrics.sets++;
    this.metrics.size++;
  }

  recordDelete() {
    this.metrics.deletes++;
    this.metrics.size = Math.max(0, this.metrics.size - 1);
  }

  updateHitRate() {
    const total = this.metrics.hits + this.metrics.misses;
    this.metrics.hitRate = total > 0 ? this.metrics.hits / total : 0;
  }

  getMetrics() {
    return {
      ...this.metrics,
      totalRequests: this.metrics.hits + this.metrics.misses,
      timestamp: new Date().toISOString()
    };
  }

  getStats() {
    return {
      hitRate: this.metrics.hitRate,
      size: this.metrics.size,
      efficiency: this.calculateEfficiency(),
      timestamp: new Date().toISOString()
    };
  }

  calculateEfficiency() {
    const totalOperations = this.metrics.sets + this.metrics.deletes;
    return totalOperations > 0 ? this.metrics.hits / totalOperations : 0;
  }
}

// Redis cache with monitoring
class MonitoredRedisCache {
  constructor(redisClient) {
    this.client = redisClient;
    this.monitor = new CacheMonitor();
  }

  async get(key) {
    try {
      const value = await this.client.get(key);
      if (value) {
        this.monitor.recordHit();
        return JSON.parse(value);
      } else {
        this.monitor.recordMiss();
        return null;
      }
    } catch (error) {
      this.monitor.recordMiss();
      throw error;
    }
  }

  async set(key, value, ttl = 300) {
    try {
      await this.client.setEx(key, ttl, JSON.stringify(value));
      this.monitor.recordSet();
    } catch (error) {
      throw error;
    }
  }

  async del(key) {
    try {
      await this.client.del(key);
      this.monitor.recordDelete();
    } catch (error) {
    }
  }

  getMetrics() {
    return this.monitor.getMetrics();
  }
}
```

## 🚨 Alerting System

### Alert Configuration

```javascript
// alert-system.js
class AlertSystem {
  constructor() {
    this.alerts = [];
    this.thresholds = {
      responseTime: 1000, // ms
      errorRate: 0.05,    // 5%
      cacheHitRate: 0.8,  // 80%
      uptime: 0.99        // 99%
    };
  }

  addAlert(type, message, severity = 'warning') {
    const alert = {
      id: this.generateId(),
      type,
      message,
      severity,
      timestamp: new Date().toISOString(),
      acknowledged: false
    };

    this.alerts.push(alert);
    this.notify(alert);
  }

  checkThresholds(metrics) {
    // Response time alert
    if (metrics.averageResponseTime > this.thresholds.responseTime) {
      this.addAlert('performance', `High response time: ${metrics.averageResponseTime}ms`, 'warning');
    }

    // Error rate alert
    if (metrics.errorRate > this.thresholds.errorRate) {
      this.addAlert('error', `High error rate: ${(metrics.errorRate * 100).toFixed(2)}%`, 'critical');
    }

    // Cache hit rate alert
    if (metrics.cacheHitRate < this.thresholds.cacheHitRate) {
      this.addAlert('cache', `Low cache hit rate: ${(metrics.cacheHitRate * 100).toFixed(2)}%`, 'warning');
    }

    // Uptime alert
    if (metrics.uptime < this.thresholds.uptime) {
      this.addAlert('uptime', `Low uptime: ${(metrics.uptime * 100).toFixed(2)}%`, 'critical');
    }
  }

  async notify(alert) {
    // Send to configured channels
    await Promise.all([
      this.sendToWebhook(alert),
      this.sendToEmail(alert),
      this.sendToSlack(alert)
    ]);
  }

  async sendToWebhook(alert) {
    if (!process.env.ALERT_WEBHOOK_URL) return;

    try {
      await fetch(process.env.ALERT_WEBHOOK_URL, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(alert)
      });
    } catch (error) {
      console.error('Webhook notification failed:', error);
    }
  }

  async sendToEmail(alert) {
    if (!process.env.SMTP_CONFIG) return;

    // Implement email sending logic
    console.log(`📧 Alert Email: ${alert.severity.toUpperCase()} - ${alert.message}`);
  }

  async sendToSlack(alert) {
    if (!process.env.SLACK_WEBHOOK_URL) return;

    const payload = {
      text: `🚨 *${alert.severity.toUpperCase()} Alert*\n${alert.message}`,
      attachments: [{
        color: this.getSeverityColor(alert.severity),
        fields: [
          { title: 'Type', value: alert.type, short: true },
          { title: 'Time', value: alert.timestamp, short: true }
        ]
      }]
    };

    try {
      await fetch(process.env.SLACK_WEBHOOK_URL, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(payload)
      });
    } catch (error) {
      console.error('Slack notification failed:', error);
    }
  }

  getSeverityColor(severity) {
    const colors = {
      critical: 'danger',
      warning: 'warning',
      info: 'good'
    };
    return colors[severity] || 'good';
  }

  generateId() {
    return Date.now().toString(36) + Math.random().toString(36).substr(2);
  }

  getAlerts(filter = {}) {
    let filtered = this.alerts;

    if (filter.severity) {
      filtered = filtered.filter(alert => alert.severity === filter.severity);
    }

    if (filter.type) {
      filtered = filtered.filter(alert => alert.type === filter.type);
    }

    if (filter.acknowledged !== undefined) {
      filtered = filtered.filter(alert => alert.acknowledged === filter.acknowledged);
    }

    return filtered;
  }

  acknowledgeAlert(alertId) {
    const alert = this.alerts.find(a => a.id === alertId);
    if (alert) {
      alert.acknowledged = true;
      alert.acknowledgedAt = new Date().toISOString();
    }
  }
}
```

### Automated Alerting

```javascript
// automated-alerts.js
const alertSystem = new AlertSystem();

// Monitor system every 5 minutes
setInterval(async () => {
  try {
    const metrics = await collectSystemMetrics();

    // Check thresholds
    alertSystem.checkThresholds(metrics);

    // Clean up old alerts (keep last 24 hours)
    const oneDayAgo = new Date(Date.now() - 24 * 60 * 60 * 1000);
    alertSystem.alerts = alertSystem.alerts.filter(
      alert => new Date(alert.timestamp) > oneDayAgo
    );

  } catch (error) {
    console.error('Alert monitoring failed:', error);
    alertSystem.addAlert('system', 'Alert monitoring system failed', 'critical');
  }
}, 5 * 60 * 1000); // 5 minutes

async function collectSystemMetrics() {
  // Collect metrics from various sources
  const [health, performance, cache] = await Promise.all([
    checkSystemHealth(),
    getPerformanceMetrics(),
    getCacheMetrics()
  ]);

  return {
    ...health,
    ...performance,
    ...cache
  };
}
```

## 📊 Dashboard and Visualization

### Monitoring Dashboard

```javascript
// dashboard.js
class MonitoringDashboard {
  constructor(containerId) {
    this.container = document.getElementById(containerId);
    this.charts = {};
    this.metrics = {};
    this.updateInterval = 30000; // 30 seconds
  }

  async initialize() {
    await this.createCharts();
    this.startUpdates();
  }

  async createCharts() {
    // System Health Chart
    this.createHealthChart();

    // Performance Chart
    this.createPerformanceChart();

    // Cache Efficiency Chart
    this.createCacheChart();

    // Error Rate Chart
    this.createErrorChart();
  }

  createHealthChart() {
    const ctx = document.createElement('canvas');
    ctx.id = 'healthChart';
    this.container.appendChild(ctx);

    this.charts.health = new Chart(ctx, {
      type: 'doughnut',
      data: {
        labels: ['Healthy', 'Warning', 'Critical'],
        datasets: [{
          data: [85, 10, 5],
          backgroundColor: ['#10b981', '#f59e0b', '#ef4444']
        }]
      },
      options: {
        responsive: true,
        plugins: {
          title: {
            display: true,
            text: 'System Health Overview'
          }
        }
      }
    });
  }

  createPerformanceChart() {
    const ctx = document.createElement('canvas');
    ctx.id = 'performanceChart';
    this.container.appendChild(ctx);

    this.charts.performance = new Chart(ctx, {
      type: 'line',
      data: {
        labels: [],
        datasets: [{
          label: 'Response Time (ms)',
          data: [],
          borderColor: '#3b82f6',
          backgroundColor: '#3b82f640'
        }]
      },
      options: {
        responsive: true,
        plugins: {
          title: {
            display: true,
            text: 'Search Performance'
          }
        },
        scales: {
          y: {
            beginAtZero: true
          }
        }
      }
    });
  }

  async updateCharts() {
    try {
      const metrics = await this.fetchMetrics();

      // Update health chart
      this.updateHealthChart(metrics.health);

      // Update performance chart
      this.updatePerformanceChart(metrics.performance);

      // Update cache chart
      this.updateCacheChart(metrics.cache);

      // Update error chart
      this.updateErrorChart(metrics.errors);

    } catch (error) {
      console.error('Chart update failed:', error);
    }
  }

  async fetchMetrics() {
    const response = await fetch('/api/monitoring/metrics');
    return await response.json();
  }

  updateHealthChart(healthData) {
    this.charts.health.data.datasets[0].data = [
      healthData.healthy,
      healthData.warning,
      healthData.critical
    ];
    this.charts.health.update();
  }

  updatePerformanceChart(performanceData) {
    const chart = this.charts.performance;
    const now = new Date().toLocaleTimeString();

    chart.data.labels.push(now);
    chart.data.datasets[0].data.push(performanceData.responseTime);

    // Keep only last 20 data points
    if (chart.data.labels.length > 20) {
      chart.data.labels.shift();
      chart.data.datasets[0].data.shift();
    }

    chart.update();
  }

  startUpdates() {
    setInterval(() => this.updateCharts(), this.updateInterval);
  }
}

// Initialize dashboard
const dashboard = new MonitoringDashboard('monitoring-container');
dashboard.initialize();
```

### Real-time Metrics Display

```html
<!-- monitoring-dashboard.html -->
<div id="monitoring-container">
  <div class="metrics-grid">
    <div class="metric-card">
      <h3>System Health</h3>
      <canvas id="healthChart"></canvas>
    </div>

    <div class="metric-card">
      <h3>Response Time</h3>
      <canvas id="performanceChart"></canvas>
    </div>

    <div class="metric-card">
      <h3>Cache Efficiency</h3>
      <canvas id="cacheChart"></canvas>
    </div>

    <div class="metric-card">
      <h3>Error Rate</h3>
      <canvas id="errorChart"></canvas>
    </div>
  </div>

  <div class="alerts-section">
    <h3>Active Alerts</h3>
    <div id="alerts-list"></div>
  </div>
</div>

<style>
.metrics-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
  margin-bottom: 30px;
}

.metric-card {
  background: white;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.metric-card h3 {
  margin: 0 0 15px 0;
  color: #333;
}

.alerts-section {
  background: white;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.alert {
  padding: 10px;
  margin: 5px 0;
  border-radius: 4px;
  border-left: 4px solid;
}

.alert.critical { border-left-color: #ef4444; background: #fef2f2; }
.alert.warning { border-left-color: #f59e0b; background: #fffbeb; }
.alert.info { border-left-color: #3b82f6; background: #eff6ff; }
</style>
```

## 🔧 Maintenance Tasks

### Automated Maintenance

```javascript
// maintenance-scheduler.js
class MaintenanceScheduler {
  constructor() {
    this.tasks = new Map();
    this.scheduleMaintenance();
  }

  scheduleMaintenance() {
    // Daily tasks
    this.scheduleTask('daily-cache-cleanup', '0 2 * * *', () => this.cleanupCache());
    this.scheduleTask('daily-log-rotation', '0 3 * * *', () => this.rotateLogs());
    this.scheduleTask('daily-backup', '0 4 * * *', () => this.createBackup());

    // Weekly tasks
    this.scheduleTask('weekly-db-optimization', '0 5 * * 0', () => this.optimizeDatabase());
    this.scheduleTask('weekly-security-scan', '0 6 * * 0', () => this.securityScan());

    // Monthly tasks
    this.scheduleTask('monthly-report', '0 7 1 * *', () => this.generateMonthlyReport());
  }

  scheduleTask(name, cronExpression, task) {
    // Simple cron-like scheduler (in production, use node-cron)
    const interval = this.parseCronExpression(cronExpression);
    this.tasks.set(name, {
      task,
      interval,
      lastRun: null,
      nextRun: Date.now() + interval
    });

    setInterval(() => this.checkAndRunTask(name), 60000); // Check every minute
  }

  async checkAndRunTask(name) {
    const taskInfo = this.tasks.get(name);
    if (!taskInfo) return;

    if (Date.now() >= taskInfo.nextRun) {
      try {
        console.log(`🔧 Running maintenance task: ${name}`);
        await taskInfo.task();
        taskInfo.lastRun = Date.now();
        taskInfo.nextRun = Date.now() + taskInfo.interval;
        console.log(`✅ Maintenance task completed: ${name}`);
      } catch (error) {
        console.error(`❌ Maintenance task failed: ${name}`, error);
        // Send alert
        alertSystem.addAlert('maintenance', `Task ${name} failed: ${error.message}`, 'critical');
      }
    }
  }

  parseCronExpression(cron) {
    // Simple cron parser (implement full cron logic for production)
    const parts = cron.split(' ');
    if (parts[0] === '0' && parts[1] === '2') return 24 * 60 * 60 * 1000; // Daily at 2 AM
    if (parts[0] === '0' && parts[1] === '3') return 24 * 60 * 60 * 1000; // Daily at 3 AM
    return 60 * 60 * 1000; // Default 1 hour
  }

  async cleanupCache() {
    // Implement cache cleanup
    console.log('🧹 Cleaning up cache...');
    // Remove expired entries
    // Optimize memory usage
  }

  async rotateLogs() {
    // Implement log rotation
    console.log('📝 Rotating logs...');
    // Compress old logs
    // Remove very old logs
  }

  async createBackup() {
    // Implement backup creation
    console.log('💾 Creating backup...');
    // Database backup
    // File system backup
    // Upload to storage
  }

  async optimizeDatabase() {
    // Implement database optimization
    console.log('🔧 Optimizing database...');
    // Run VACUUM
    // Reindex tables
    // Update statistics
  }

  async securityScan() {
    // Implement security scan
    console.log('🔒 Running security scan...');
    // Check for vulnerabilities
    // Scan for malware
    // Review permissions
  }

  async generateMonthlyReport() {
    // Implement monthly reporting
    console.log('📊 Generating monthly report...');
    // Collect metrics
    // Generate charts
    // Send to stakeholders
  }

  getTaskStatus() {
    const status = {};
    for (const [name, taskInfo] of this.tasks) {
      status[name] = {
        lastRun: taskInfo.lastRun ? new Date(taskInfo.lastRun).toISOString() : null,
        nextRun: new Date(taskInfo.nextRun).toISOString(),
        status: taskInfo.lastRun ? 'completed' : 'pending'
      };
    }
    return status;
  }
}

// Start maintenance scheduler
const maintenanceScheduler = new MaintenanceScheduler();
```

### Manual Maintenance Commands

```bash
# Cache management
npm run cache:clear          # Clear all cache
npm run cache:warm           # Warm up cache with popular queries
npm run cache:stats          # Show cache statistics

# Database maintenance
npm run db:backup            # Create database backup
npm run db:optimize          # Optimize database performance
npm run db:migrate           # Run pending migrations

# Log management
npm run logs:rotate          # Rotate log files
npm run logs:archive         # Archive old logs
npm run logs:analyze         # Analyze log patterns

# Security
npm run security:scan        # Run security scan
npm run security:update      # Update security dependencies
npm run security:audit       # Audit security configuration

# Performance
npm run perf:test            # Run performance tests
npm run perf:benchmark       # Run benchmark tests
npm run perf:optimize        # Apply performance optimizations
```

## 📈 Analytics and Reporting

### Usage Analytics

```javascript
// analytics.js
class UsageAnalytics {
  constructor() {
    this.events = [];
    this.sessionId = this.generateSessionId();
  }

  trackEvent(eventType, data) {
    const event = {
      sessionId: this.sessionId,
      eventType,
      data,
      timestamp: new Date().toISOString(),
      userAgent: navigator.userAgent,
      url: window.location.href
    };

    this.events.push(event);
    this.sendToAnalytics(event);
  }

  trackSearch(query, results, responseTime) {
    this.trackEvent('search', {
      query,
      resultCount: results.total,
      responseTime,
      filters: results.filters,
      cached: results.cached
    });
  }

  trackResultClick(resultId, resultData) {
    this.trackEvent('result_click', {
      resultId,
      resultType: resultData.assetType,
      position: resultData.position,
      creator: resultData.creator
    });
  }

  trackError(errorType, errorMessage, context) {
    this.trackEvent('error', {
      errorType,
      errorMessage,
      context,
      stack: error.stack
    });
  }

  async sendToAnalytics(event) {
    try {
      await fetch('/api/analytics/track', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(event)
      });
    } catch (error) {
      console.error('Analytics tracking failed:', error);
    }
  }

  generateSessionId() {
    return Date.now().toString(36) + Math.random().toString(36).substr(2);
  }

  getSessionData() {
    return {
      sessionId: this.sessionId,
      events: this.events,
      duration: Date.now() - parseInt(this.sessionId, 36),
      pageViews: this.events.filter(e => e.eventType === 'page_view').length,
      searches: this.events.filter(e => e.eventType === 'search').length
    };
  }
}

// Initialize analytics
const analytics = new UsageAnalytics();

// Track search events
function trackSearch(query, results, responseTime) {
  analytics.trackSearch(query, results, responseTime);
}

// Track result clicks
function trackResultClick(resultId, resultData) {
  analytics.trackResultClick(resultId, resultData);
}
```

### Performance Reporting

```javascript
// performance-report.js
class PerformanceReport {
  constructor() {
    this.metrics = new Map();
    this.reports = [];
  }

  async generateDailyReport() {
    const yesterday = new Date();
    yesterday.setDate(yesterday.getDate() - 1);

    const metrics = await this.collectMetrics(yesterday);
    const report = this.analyzeMetrics(metrics);

    this.reports.push({
      date: yesterday.toISOString().split('T')[0],
      metrics,
      analysis: report,
      generatedAt: new Date().toISOString()
    });

    await this.saveReport(report);
    await this.sendReport(report);

    return report;
  }

  async collectMetrics(date) {
    // Collect metrics from various sources
    const [searchMetrics, cacheMetrics, errorMetrics] = await Promise.all([
      this.getSearchMetrics(date),
      this.getCacheMetrics(date),
      this.getErrorMetrics(date)
    ]);

    return {
      search: searchMetrics,
      cache: cacheMetrics,
      errors: errorMetrics,
      date: date.toISOString().split('T')[0]
    };
  }

  analyzeMetrics(metrics) {
    return {
      summary: {
        totalSearches: metrics.search.total,
        averageResponseTime: metrics.search.averageResponseTime,
        cacheHitRate: metrics.cache.hitRate,
        errorRate: metrics.errors.rate
      },
      trends: this.calculateTrends(metrics),
      alerts: this.identifyIssues(metrics),
      recommendations: this.generateRecommendations(metrics)
    };
  }

  calculateTrends(metrics) {
    // Compare with previous days
    const previousDay = this.reports[this.reports.length - 1];
    if (!previousDay) return {};

    return {
      searchGrowth: ((metrics.search.total - previousDay.metrics.search.total) /
                    previousDay.metrics.search.total) * 100,
      responseTimeChange: metrics.search.averageResponseTime -
                         previousDay.metrics.search.averageResponseTime,
      cacheEfficiencyChange: metrics.cache.hitRate - previousDay.metrics.cache.hitRate
    };
  }

  identifyIssues(metrics) {
    const alerts = [];

    if (metrics.search.averageResponseTime > 1000) {
      alerts.push({
        type: 'performance',
        severity: 'warning',
        message: 'Average response time is above 1 second'
      });
    }

    if (metrics.cache.hitRate < 0.8) {
      alerts.push({
        type: 'cache',
        severity: 'warning',
        message: 'Cache hit rate is below 80%'
      });
    }

    if (metrics.errors.rate > 0.05) {
      alerts.push({
        type: 'errors',
        severity: 'critical',
        message: 'Error rate is above 5%'
      });
    }

    return alerts;
  }

  generateRecommendations(metrics) {
    const recommendations = [];

    if (metrics.search.averageResponseTime > 1000) {
      recommendations.push('Consider optimizing database queries or adding more cache layers');
    }

    if (metrics.cache.hitRate < 0.8) {
      recommendations.push('Review cache strategy and increase cache size if needed');
    }

    if (metrics.errors.rate > 0.05) {
      recommendations.push('Investigate error causes and implement better error handling');
    }

    return recommendations;
  }

  async saveReport(report) {
    // Save to database or file system
    console.log('💾 Saving performance report...');
  }

  async sendReport(report) {
    // Send via email or webhook
    console.log('📧 Sending performance report...');
  }

  getReports(limit = 30) {
    return this.reports.slice(-limit);
  }
}

// Generate daily reports
const performanceReport = new PerformanceReport();

// Schedule daily report generation
setInterval(() => {
  performanceReport.generateDailyReport();
}, 24 * 60 * 60 * 1000); // Daily
```

---

## 🎯 Monitoring Checklist

### Daily Checks
- [ ] System health status
- [ ] Response time metrics
- [ ] Error rates and alerts
- [ ] Cache performance
- [ ] AO process status

### Weekly Reviews
- [ ] Performance trends
- [ ] User behavior analytics
- [ ] Security scan results
- [ ] Backup verification
- [ ] Log analysis

### Monthly Tasks
- [ ] Generate performance reports
- [ ] Review system capacity
- [ ] Update monitoring thresholds
- [ ] Plan infrastructure improvements
- [ ] Security assessment

---

## 📞 Support & Resources

**📊 Monitoring Support:**
- 📖 [Deployment Guide](deployment.md)
- 🏗️ [Architecture Overview](architecture.md)
- 💬 [Community Discord](https://discord.gg/permasearch)

**🔗 Additional Resources:**
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Tutorials](https://grafana.com/tutorials/)
- [AO Monitoring Tools](https://ao.arweave.dev/docs/monitoring)

**📧 Professional Services:**
- 24/7 monitoring setup
- Custom dashboard development
- Performance optimization
- Emergency response planning

---

**🚀 Your PermaSearch system is now fully monitored and optimized for peak performance!** Regularly review these metrics and alerts to ensure continued excellent service delivery.
