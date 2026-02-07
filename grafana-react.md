# Grafana for React - Complete Guide

## Table of Contents
1. [Grafana Fundamentals](#grafana-fundamentals)
2. [Grafana Architecture](#grafana-architecture)
3. [Data Sources](#data-sources)
4. [Dashboards & Visualization](#dashboards--visualization)
5. [React Integration](#react-integration)
6. [Alerting & Notifications](#alerting--notifications)
7. [Advanced Features](#advanced-features)
8. [Implementation Guide](#implementation-guide)
9. [Interview Q&A](#interview-qa)

---

## Grafana Fundamentals

### What is Grafana?
Grafana is an open-source visualization and monitoring platform that connects to various data sources and presents data through interactive dashboards. It enables real-time monitoring, alerting, and analysis of metrics.

### Key Features
- **Multi-source Support**: Connect to Prometheus, Elasticsearch, InfluxDB, CloudWatch, etc.
- **Interactive Dashboards**: Create custom dashboards with multiple panels
- **Real-time Monitoring**: Live data visualization and updates
- **Alerting**: Configure alerts based on metric thresholds
- **Annotations**: Add events/notes to dashboards
- **Team & Organization**: Multi-user support with role-based access
- **Plugins**: Extend functionality with community plugins
- **API**: Programmatic dashboard and organization management
- **Cloud & Self-hosted**: Both deployment options available

### Why Use Grafana?
- **Unified View**: Single pane of glass for all metrics
- **Flexibility**: Support for multiple data sources
- **Powerful Querying**: PromQL, KQL, SQL for data extraction
- **Beautiful Visualizations**: Various chart types and customizations
- **Alerting**: Proactive issue detection
- **Open Source**: Free, community-supported option

---

## Grafana Architecture

### Core Components

```
┌─────────────────────────────────────────────┐
│         Grafana Server                      │
├─────────────────────────────────────────────┤
│  ┌──────────────────────────────────────┐   │
│  │  Grafana API                         │   │
│  │  (Dashboards, Datasources, Alerts)   │   │
│  └──────────────────────────────────────┘   │
├─────────────────────────────────────────────┤
│  ┌──────────────┐   ┌──────────────────┐   │
│  │  Database    │   │  Authentication  │   │
│  │  (SQLite/    │   │  (LDAP/OAuth)    │   │
│  │   Postgres)  │   │                  │   │
│  └──────────────┘   └──────────────────┘   │
├─────────────────────────────────────────────┤
│  ┌──────────────────────────────────────┐   │
│  │  Data Sources                        │   │
│  │  Prometheus │ InfluxDB │ Elasticsearch  │
│  │  CloudWatch │ MySQL    │ Datadog    │   │
│  └──────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
```

### Data Flow

```
Metrics Collection → Data Source → Grafana Dashboard → Visualization
      (App)       (Prometheus)    (API/UI)          (Browser)
```

---

## Data Sources

### Prometheus
Time-series database for metrics.

```javascript
// Configuration
{
  datasource: {
    type: 'prometheus',
    url: 'http://localhost:9090',
    access: 'proxy'
  }
}

// Query Example (PromQL)
rate(http_request_duration_seconds_bucket[5m])
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

### InfluxDB
Time-series database with flexible data model.

```javascript
// Configuration
{
  datasource: {
    type: 'influxdb',
    url: 'http://localhost:8086',
    database: 'mydb',
    access: 'proxy'
  }
}

// Query Example (InfluxQL)
SELECT mean("value") FROM "measurement" WHERE time > now() - 1h GROUP BY time(5m)
```

### Elasticsearch
Log and search engine data source.

```javascript
// Configuration
{
  datasource: {
    type: 'elasticsearch',
    url: 'http://localhost:9200',
    index: 'logs',
    access: 'proxy'
  }
}

// Query: Aggregations and filtering
```

### CloudWatch
AWS monitoring data source.

```javascript
// Configuration
{
  datasource: {
    type: 'cloudwatch',
    region: 'us-east-1',
    access: 'proxy'
  }
}

// Metrics: EC2, RDS, Lambda, etc.
```

### MySQL/PostgreSQL
SQL databases for custom metrics.

```javascript
// Configuration
{
  datasource: {
    type: 'mysql' || 'postgres',
    host: 'localhost',
    database: 'metrics_db',
    user: 'grafana',
    password: 'password'
  }
}

// Query
SELECT timestamp, metric_name, value FROM metrics WHERE timestamp > NOW() - INTERVAL '1 hour'
```

### Datadog
Monitoring and analytics platform.

```javascript
// Configuration
{
  datasource: {
    type: 'datadog',
    apiKey: 'YOUR_API_KEY',
    appKey: 'YOUR_APP_KEY'
  }
}
```

### Graphite
Metrics storage and visualization.

```javascript
// Configuration
{
  datasource: {
    type: 'graphite',
    url: 'http://localhost:8080',
    access: 'proxy'
  }
}
```

---

## Dashboards & Visualization

### Dashboard JSON Model
```json
{
  "dashboard": {
    "title": "Application Metrics",
    "description": "Real-time application monitoring",
    "tags": ["app", "monitoring"],
    "timezone": "browser",
    "panels": [
      {
        "id": 1,
        "title": "Request Rate",
        "type": "graph",
        "gridPos": {
          "x": 0,
          "y": 0,
          "w": 12,
          "h": 8
        },
        "targets": [
          {
            "refId": "A",
            "expr": "rate(http_requests_total[5m])"
          }
        ]
      }
    ]
  }
}
```

### Panel Types

#### 1. Graph Panel
```javascript
{
  type: 'graph',
  title: 'Response Time Trend',
  targets: [{
    expr: 'histogram_quantile(0.95, rate(response_time[5m]))'
  }],
  options: {
    legend: { show: true },
    tooltip: { shared: true }
  }
}
```

#### 2. Stat Panel
```javascript
{
  type: 'stat',
  title: 'Error Rate',
  targets: [{
    expr: 'rate(errors_total[5m])'
  }],
  options: {
    graphMode: 'area',
    orientation: 'auto',
    textMode: 'auto'
  }
}
```

#### 3. Gauge Panel
```javascript
{
  type: 'gauge',
  title: 'CPU Usage',
  targets: [{
    expr: 'node_cpu_seconds_total'
  }],
  options: {
    min: 0,
    max: 100,
    thresholds: {
      mode: 'absolute',
      steps: [
        { color: 'green', value: 0 },
        { color: 'yellow', value: 70 },
        { color: 'red', value: 90 }
      ]
    }
  }
}
```

#### 4. Table Panel
```javascript
{
  type: 'table',
  title: 'Logs',
  targets: [{
    query: 'SELECT timestamp, message, level FROM logs LIMIT 100'
  }]
}
```

#### 5. Heatmap Panel
```javascript
{
  type: 'heatmap',
  title: 'Request Latency Heatmap',
  targets: [{
    expr: 'rate(request_duration_seconds_bucket[5m])'
  }]
}
```

#### 6. PieChart Panel
```javascript
{
  type: 'piechart',
  title: 'Traffic by Source',
  targets: [{
    expr: 'sum by (source) (requests_total)'
  }]
}
```

### Dashboard Variables

```json
{
  "templating": {
    "list": [
      {
        "name": "datasource",
        "type": "datasource",
        "datasource": "prometheus"
      },
      {
        "name": "environment",
        "type": "query",
        "datasource": "prometheus",
        "query": "label_values(up, env)"
      },
      {
        "name": "threshold",
        "type": "custom",
        "options": ["0.1", "0.5", "1.0"]
      }
    ]
  }
}
```

---

## React Integration

### Setting Up Grafana with React

#### Option 1: Embed Grafana Dashboards

```javascript
// src/components/GrafanaDashboard.js
import React, { useEffect } from 'react';

const GrafanaDashboard = ({ dashboardId, orgId = 1 }) => {
  const grafanaUrl = process.env.REACT_APP_GRAFANA_URL;
  
  useEffect(() => {
    const script = document.createElement('script');
    script.src = `${grafanaUrl}/public/app/plugins/datasource/grafana-plugin-sdk/dist/plugin-sdk.js`;
    document.body.appendChild(script);
  }, [grafanaUrl]);

  return (
    <iframe
      src={`${grafanaUrl}/d/${dashboardId}?orgId=${orgId}&kiosk`}
      width="100%"
      height="600px"
      frameBorder="0"
      title="Grafana Dashboard"
    />
  );
};

export default GrafanaDashboard;
```

#### Option 2: Use Grafana API

```javascript
// src/services/grafana.js
import axios from 'axios';

const GRAFANA_URL = process.env.REACT_APP_GRAFANA_URL;
const API_TOKEN = process.env.REACT_APP_GRAFANA_API_TOKEN;

const grafanaAPI = axios.create({
  baseURL: `${GRAFANA_URL}/api`,
  headers: {
    'Authorization': `Bearer ${API_TOKEN}`,
    'Content-Type': 'application/json'
  }
});

export const grafanaService = {
  // Get dashboards
  async getDashboards() {
    const response = await grafanaAPI.get('/search?type=dash-db');
    return response.data;
  },

  // Get single dashboard
  async getDashboard(uid) {
    const response = await grafanaAPI.get(`/dashboards/uid/${uid}`);
    return response.data;
  },

  // Create dashboard
  async createDashboard(dashboard) {
    const response = await grafanaAPI.post('/dashboards/db', {
      dashboard,
      overwrite: false
    });
    return response.data;
  },

  // Update dashboard
  async updateDashboard(uid, dashboard) {
    const response = await grafanaAPI.put(`/dashboards/uid/${uid}`, {
      dashboard,
      overwrite: true
    });
    return response.data;
  },

  // Delete dashboard
  async deleteDashboard(uid) {
    return await grafanaAPI.delete(`/dashboards/uid/${uid}`);
  },

  // Get data source
  async getDataSource(name) {
    const response = await grafanaAPI.get(`/datasources/name/${name}`);
    return response.data;
  },

  // Query metrics
  async queryMetrics(datasourceId, query, from, to) {
    const response = await grafanaAPI.post('/tsdb/query', {
      queries: [{
        datasourceId,
        target: query,
        refId: 'A'
      }],
      from,
      to
    });
    return response.data;
  },

  // Get alerts
  async getAlerts() {
    const response = await grafanaAPI.get('/alerts');
    return response.data;
  },

  // Create alert
  async createAlert(alert) {
    const response = await grafanaAPI.post('/alerts', alert);
    return response.data;
  }
};
```

### React Hook for Grafana

```javascript
// src/hooks/useGrafana.js
import { useState, useEffect } from 'react';
import { grafanaService } from '../services/grafana';

export const useGrafanaDashboards = () => {
  const [dashboards, setDashboards] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchDashboards = async () => {
      try {
        const data = await grafanaService.getDashboards();
        setDashboards(data);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchDashboards();
  }, []);

  return { dashboards, loading, error };
};

export const useGrafanaMetrics = (datasourceId, query, interval = 5000) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchMetrics = async () => {
      try {
        const now = new Date();
        const from = new Date(now - 1 * 60 * 60 * 1000); // 1 hour ago
        
        const response = await grafanaService.queryMetrics(
          datasourceId,
          query,
          from.toISOString(),
          now.toISOString()
        );
        setData(response);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchMetrics();
    const timer = setInterval(fetchMetrics, interval);
    
    return () => clearInterval(timer);
  }, [datasourceId, query, interval]);

  return { data, loading, error };
};

export const useGrafanaAlerts = () => {
  const [alerts, setAlerts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchAlerts = async () => {
      try {
        const data = await grafanaService.getAlerts();
        setAlerts(data);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchAlerts();
    const timer = setInterval(fetchAlerts, 30000); // Refresh every 30s
    
    return () => clearInterval(timer);
  }, []);

  return { alerts, loading, error };
};
```

### Using Hooks in Components

```javascript
// src/components/MetricsDashboard.js
import React from 'react';
import { useGrafanaMetrics, useGrafanaAlerts } from '../hooks/useGrafana';

function MetricsDashboard() {
  const { data: metrics, loading, error } = useGrafanaMetrics(
    1, // datasourceId
    'rate(http_requests_total[5m])', // query
    5000 // refresh interval
  );

  const { alerts } = useGrafanaAlerts();

  if (loading) return <div>Loading metrics...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div className="dashboard">
      <h1>Application Metrics</h1>
      
      <div className="metrics">
        {metrics && metrics.data.results.map((result, idx) => (
          <div key={idx} className="metric-card">
            <h3>{result.target}</h3>
            <p>{result.datapoints[result.datapoints.length - 1][0]} req/s</p>
          </div>
        ))}
      </div>

      <div className="alerts">
        <h2>Active Alerts ({alerts.length})</h2>
        {alerts.map(alert => (
          <div key={alert.id} className={`alert alert-${alert.state}`}>
            <h4>{alert.title}</h4>
            <p>{alert.message}</p>
          </div>
        ))}
      </div>
    </div>
  );
}

export default MetricsDashboard;
```

---

## Alerting & Notifications

### Alert Configuration

```javascript
// Alert rules
{
  name: 'High Error Rate',
  condition: 'avg() > 0.05',
  data: [{
    refId: 'A',
    queryType: '',
    model: {
      expr: 'rate(errors_total[5m])'
    }
  }],
  noDataState: 'NoData',
  execErrState: 'Alerting',
  for: '5m',
  annotations: {
    description: 'Error rate exceeded 5%',
    runbook_url: 'https://wiki.example.com/runbooks/high-error-rate'
  },
  labels: {
    severity: 'critical'
  }
}
```

### Notification Channels

```javascript
// Webhook notification
{
  type: 'webhook',
  name: 'Slack Webhook',
  settings: {
    url: 'https://hooks.slack.com/services/YOUR/WEBHOOK/URL',
    httpMethod: 'POST'
  }
}

// Email notification
{
  type: 'email',
  name: 'Admin Email',
  settings: {
    addresses: 'admin@example.com;ops@example.com'
  }
}

// PagerDuty
{
  type: 'pagerduty',
  name: 'PagerDuty',
  settings: {
    integrationKey: 'YOUR_INTEGRATION_KEY'
  }
}

// Slack
{
  type: 'slack',
  name: 'Slack Channel',
  settings: {
    url: 'YOUR_SLACK_WEBHOOK_URL',
    channel: '#alerts'
  }
}
```

### Alert State Management

```javascript
// src/hooks/useAlertManagement.js
import { useState } from 'react';
import { grafanaService } from '../services/grafana';

export const useAlertManagement = () => {
  const [alerts, setAlerts] = useState([]);

  const createAlert = async (alertConfig) => {
    const result = await grafanaService.createAlert(alertConfig);
    setAlerts([...alerts, result]);
    return result;
  };

  const updateAlert = async (alertId, updates) => {
    const updated = await grafanaService.updateAlert(alertId, updates);
    setAlerts(alerts.map(a => a.id === alertId ? updated : a));
    return updated;
  };

  const deleteAlert = async (alertId) => {
    await grafanaService.deleteAlert(alertId);
    setAlerts(alerts.filter(a => a.id !== alertId));
  };

  const pauseAlert = async (alertId) => {
    return updateAlert(alertId, { state: 'paused' });
  };

  const resumeAlert = async (alertId) => {
    return updateAlert(alertId, { state: 'alerting' });
  };

  return {
    alerts,
    createAlert,
    updateAlert,
    deleteAlert,
    pauseAlert,
    resumeAlert
  };
};
```

---

## Advanced Features

### 1. Custom Plugins

```javascript
// src/grafana-plugins/custom-panel.js
export const plugin = {
  id: 'custom-panel',
  name: 'Custom React Panel',
  type: 'panel',
  
  plugin: {
    PanelComponent: function({ data, options }) {
      return (
        <div>
          {data.series.map(series => (
            <div key={series.name}>
              <h3>{series.name}</h3>
              {series.fields.map(field => (
                <div key={field.name}>{field.name}</div>
              ))}
            </div>
          ))}
        </div>
      );
    },
    
    options: {
      title: 'Custom Panel Options',
      properties: {
        showLegend: { type: 'boolean' },
        aggregation: { type: 'string', options: ['sum', 'avg', 'max'] }
      }
    }
  }
};
```

### 2. Custom Data Sources

```javascript
// src/grafana-datasources/custom-datasource.js
export const plugin = {
  id: 'custom-datasource',
  name: 'Custom Data Source',
  type: 'datasource',
  
  plugin: {
    query: async function(options) {
      const { targets, from, to } = options;
      
      return targets.map(target => ({
        target: target.target,
        datapoints: [[10, from], [20, from + 60000]],
        refId: target.refId
      }));
    },
    
    testDatasource: async function() {
      return { status: 'success', message: 'Data source is working' };
    }
  }
};
```

### 3. Annotations

```javascript
// Add annotations to dashboards
{
  time: Date.now(),
  timeEnd: Date.now() + 3600000,
  tags: ['deployment', 'v1.0.0'],
  text: 'Deployed v1.0.0 to production',
  dashboardId: 1,
  panelId: 2
}
```

### 4. Templating & Variables

```javascript
// Dashboard variables
{
  name: 'instance',
  type: 'query',
  datasource: 'Prometheus',
  query: 'label_values(up, instance)',
  multi: true,
  includeAll: true
}

// Usage in queries
const query = `rate(http_requests_total{instance="$instance"}[5m])`;
```

### 5. Provisioning

```yaml
# /etc/grafana/provisioning/dashboards/dashboards.yml
apiVersion: 1

providers:
  - name: 'default'
    orgId: 1
    folder: ''
    type: file
    disableDeletion: false
    editable: true
    options:
      path: /var/lib/grafana/dashboards
```

### 6. Scripting & Automation

```javascript
// src/utils/grafanaAutomation.js
export const automateMetricCollection = async () => {
  const apps = ['api', 'web', 'worker'];
  
  for (const app of apps) {
    const dashboard = {
      title: `${app} Metrics`,
      panels: [
        {
          title: 'Request Rate',
          targets: [{
            expr: `rate(${app}_requests_total[5m])`
          }]
        },
        {
          title: 'Error Rate',
          targets: [{
            expr: `rate(${app}_errors_total[5m])`
          }]
        },
        {
          title: 'Response Time P95',
          targets: [{
            expr: `histogram_quantile(0.95, rate(${app}_duration_seconds_bucket[5m]))`
          }]
        }
      ]
    };

    await grafanaService.createDashboard(dashboard);
  }
};
```

---

## Implementation Guide

### Step 1: Install Grafana

```bash
# Docker
docker run -d -p 3000:3000 grafana/grafana

# Or download from https://grafana.com/grafana/download
```

### Step 2: Initial Configuration

```javascript
// 1. Access http://localhost:3000
// 2. Login (admin/admin)
// 3. Change password
// 4. Configure data sources
// 5. Create dashboards
```

### Step 3: Set Up Data Sources

```bash
curl -X POST http://localhost:3000/api/datasources \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Prometheus",
    "type": "prometheus",
    "url": "http://prometheus:9090",
    "access": "proxy",
    "isDefault": true
  }'
```

### Step 4: Create Dashboards

```javascript
// Programmatically create dashboard
const dashboard = {
  title: 'Application Dashboard',
  description: 'Real-time app metrics',
  tags: ['app', 'production'],
  timezone: 'browser',
  panels: [
    {
      id: 1,
      title: 'Request Rate',
      type: 'graph',
      gridPos: { x: 0, y: 0, w: 12, h: 8 },
      targets: [{ expr: 'rate(http_requests_total[5m])' }]
    }
  ]
};

await grafanaService.createDashboard(dashboard);
```

### Step 5: Configure Alerts

```javascript
const alert = {
  name: 'High Error Rate',
  condition: 'avg() > 0.05',
  data: [{
    refId: 'A',
    model: { expr: 'rate(errors_total[5m])' }
  }],
  'for': '5m'
};

await grafanaService.createAlert(alert);
```

### Step 6: Set Up Notifications

```bash
# Configure email
# Edit grafana.ini
[smtp]
enabled = true
host = smtp.gmail.com:587
user = your-email@gmail.com
password = your-app-password

# Or webhook/Slack
```

### Step 7: Integrate with React

```javascript
// src/App.js
import React from 'react';
import GrafanaDashboard from './components/GrafanaDashboard';
import MetricsDashboard from './components/MetricsDashboard';

function App() {
  return (
    <div className="App">
      <h1>Monitoring Dashboard</h1>
      <GrafanaDashboard dashboardId="abc123" />
      <MetricsDashboard />
    </div>
  );
}

export default App;
```

### Step 8: Monitor & Optimize

```javascript
// Monitor Grafana performance
const monitorGrafana = async () => {
  const health = await grafanaService.getHealth();
  console.log('Grafana Health:', health);
  
  const stats = await grafanaService.getStats();
  console.log('Grafana Stats:', stats);
};
```

---

## Interview Q&A

### 1. What is Grafana?
**Answer**: Grafana is an open-source visualization and monitoring platform that connects to various data sources (Prometheus, InfluxDB, Elasticsearch, etc.) and creates interactive dashboards for real-time monitoring, alerting, and analysis of metrics.

### 2. What are the key features of Grafana?
**Answer**: Multi-source support, interactive dashboards, real-time monitoring, alerting, annotations, team management, plugins, API access, and both cloud and self-hosted deployment options.

### 3. What is a data source in Grafana?
**Answer**: A data source is a connection to a backend service or database that stores metrics or logs. Examples: Prometheus, InfluxDB, Elasticsearch, CloudWatch, MySQL, PostgreSQL.

### 4. What is PromQL?
**Answer**: Prometheus Query Language used to query time-series metrics from Prometheus. Supports range vectors, instant vectors, aggregation operators, and functions.

### 5. What are the differences between Grafana and Kibana?
**Answer**: Grafana is metrics-focused with broad data source support. Kibana is log/document-focused with Elasticsearch. Grafana has better alerting, Kibana has better log analysis.

### 6. What is a dashboard in Grafana?
**Answer**: A dashboard is a collection of panels displaying metrics and logs from data sources. Panels can be graphs, tables, gauges, heatmaps, etc. Dashboards are customizable and interactive.

### 7. What are variables in Grafana dashboards?
**Answer**: Variables allow dynamic dashboard filtering. Types: query (from data source), custom, datasource, constant. Used with $ syntax in queries (e.g., $instance).

### 8. How do you create alerts in Grafana?
**Answer**: Define alert rules with conditions (thresholds), data source queries, evaluation interval, and notification channels (email, Slack, webhook, PagerDuty).

### 9. What is the difference between alerting and annotations?
**Answer**: Alerts notify when conditions are met (threshold exceeded). Annotations are events/notes added to dashboards (deployments, incidents) for context.

### 10. How do you integrate Grafana with React?
**Answer**: Use iframe embeds to display dashboards, or use Grafana API to query metrics and build custom visualizations in React components.

### 11. What is the Grafana API?
**Answer**: RESTful API for programmatic access to dashboards, data sources, alerts, users, and organizations. Requires API token authentication.

### 12. What are panels in Grafana?
**Answer**: Building blocks of dashboards. Types: Graph, Stat, Gauge, Table, Heatmap, PieChart, Bar Gauge, Text, etc.

### 13. What is the difference between Graph and Stat panels?
**Answer**: Graph panel shows time-series data over time. Stat panel shows single numerical value with optional sparkline or gauge.

### 14. How do you handle authentication in Grafana?
**Answer**: Built-in user management, LDAP, OAuth (Google, GitHub, GitLab), SAML, proxy authentication (X-Remote-User header).

### 15. What is RBAC in Grafana?
**Answer**: Role-Based Access Control. Roles: Admin, Editor, Viewer. Controls access to dashboards, data sources, and organizations.

### 16. What is a Grafana organization?
**Answer**: Isolated workspace containing users, dashboards, data sources. Multiple organizations on single Grafana instance with separate data.

### 17. What are Grafana plugins?
**Answer**: Extensions for data sources, panels, or apps. Community-created or custom. Installed via plugin marketplace or manually.

### 18. How do you deploy Grafana?
**Answer**: Docker, Kubernetes, binary installation, cloud offerings (Grafana Cloud). Requires database (SQLite, PostgreSQL, MySQL) for state.

### 19. What is provisioning in Grafana?
**Answer**: Automatic setup of data sources and dashboards via configuration files. Enables infrastructure-as-code for Grafana setup.

### 20. How do you back up Grafana?
**Answer**: Export dashboards as JSON, backup database, backup configuration files. Cloud version handled by Grafana Labs.

### 21. What are notification channels?
**Answer**: Destinations for alerts: email, Slack, webhook, PagerDuty, OpsGenie, Telegram, Discord, etc.

### 22. How do you test data source connections?
**Answer**: Use "Test" button in data source configuration. Verifies connectivity, permissions, and query syntax.

### 23. What is a Grafana snapshot?
**Answer**: Standalone dashboard snapshot sent to snapshot.raintank.io or custom server. Shareable read-only view without authentication.

### 24. How do you implement custom dashboards?
**Answer**: Use dashboard JSON model, define panels with queries, configure variables, set up templating for dynamic filtering.

### 25. What is the difference between sum and rate functions?
**Answer**: sum() returns total value. rate() returns rate of change per second. rate() is better for counters.

### 26. How do you handle missing data in Grafana?
**Answer**: Set fillParameter in panel options, use null handling in queries, or leave empty based on requirements.

### 27. What are thresholds in Grafana?
**Answer**: Values defining alert states and color coding. Absolute or percentage. Used to highlight critical values.

### 28. How do you implement drill-down in Grafana?
**Answer**: Use panel links, dynamic URLs with variables, or custom plugins for interactive navigation.

### 29. What is the difference between instant and range queries?
**Answer**: Instant query returns single data point at specific time. Range query returns data points over time period.

### 30. How do you optimize Grafana performance?
**Answer**: Use appropriate refresh intervals, limit data points, optimize queries, use caching, implement sampling, use efficient data sources.

### 31. What are label selectors in Prometheus?
**Answer**: Filter metrics by labels. Operators: =, !=, =~, !~. Example: {job="api", instance="localhost:8080"}

### 32. How do you implement multi-datasource dashboards?
**Answer**: Add multiple data sources in Grafana, select specific datasource per panel target.

### 33. What is the difference between global and local variables?
**Answer**: Global variables available across dashboard. Local variables scoped to specific panel.

### 34. How do you implement dark mode in Grafana?
**Answer**: User preference in settings, or default to dark theme in configuration. CSS-based theming system.

### 35. What are Grafana plugins ecosystem?
**Answer**: Community and official plugins for data sources, panels, applications. Extending Grafana functionality.

### 36. How do you implement custom authentication?
**Answer**: Use proxy authentication, OAuth, LDAP, or custom plugin for authentication backends.

### 37. What is the difference between alert rules and notification policies?
**Answer**: Alert rules define conditions to trigger. Notification policies route alerts to channels.

### 38. How do you handle time zones in Grafana?
**Answer**: Set timezone in dashboard (browser/UTC/specific timezone), affects time series display and queries.

### 39. What is the Grafana query editor?
**Answer**: UI for building queries for specific data sources. Visual editor with autocomplete and syntax help.

### 40. How do you implement metric relabeling?
**Answer**: Use relabel_configs in Prometheus scrape configs to rename labels, drop metrics, etc. Not Grafana feature but affects data shown.

### 41. What is a templated dashboard?
**Answer**: Dashboard using variables for filtering/grouping. Same dashboard definition works with different data selections.

### 42. How do you implement SLA tracking?
**Answer**: Create metrics tracking uptime/reliability, alert on SLA breaches, visualize SLA status on dashboards.

### 43. What are heatmaps used for?
**Answer**: Visualizing distribution of values over time. Shows concentration of values at different levels.

### 44. How do you implement cost tracking?
**Answer**: Add cost metrics as custom metrics, query from cost management system, visualize on dashboards.

### 45. What is the difference between panels and rows?
**Answer**: Rows organize panels logically. Panels are individual visualizations. Rows can collapse to save space.

### 46. How do you implement health checks in Grafana?
**Answer**: Create alert rules checking if services are up, visualize health status on dashboards.

### 47. What are logs integration features?
**Answer**: Loki data source for log aggregation, log panel for displaying logs alongside metrics.

### 48. How do you implement feature flags tracking?
**Answer**: Track feature flag state as metric, correlate with other metrics, analyze impact of feature changes.

### 49. What is the difference between synchronize time ranges?
**Answer**: Linking time ranges across panels so changing one updates others. Improves drill-down analysis.

### 50. How do you implement business metrics dashboards?
**Answer**: Define business KPIs, track as custom metrics, create dedicated dashboards for stakeholders.

### 51. What are value mappings?
**Answer**: Map numerical or text values to display alternatives. Useful for status codes, thresholds.

### 52. How do you implement Grafana in CI/CD?
**Answer**: Use Grafana API in CI/CD pipelines, automate dashboard creation, deploy with infrastructure.

### 53. What is the difference between graph and bar gauge?
**Answer**: Graph shows time-series trend. Bar gauge shows single value with bar representation and thresholds.

### 54. How do you implement team dashboards?
**Answer**: Organize dashboards by teams, set permissions per team, use shared variables across team dashboards.

### 55. What are annotations used for?
**Answer**: Mark events on dashboards (deployments, incidents, maintenance) for correlation with metric changes.

### 56. How do you implement performance baselines?
**Answer**: Track historical metrics, calculate baselines, alert on deviations from baselines.

### 57. What is the difference between query and script targets?
**Answer**: Query targets fetch from data source. Script targets generate data via JavaScript.

### 58. How do you implement cost optimization tracking?
**Answer**: Track resource usage metrics, correlate with cost, identify optimization opportunities.

### 59. What are table transformations?
**Answer**: Transform query results (filter, rename columns, calculate fields) before display.

### 60. How do you implement error budget tracking?
**Answer**: Define SLO, calculate error budget, track against budget, alert on budget exhaustion.

### 61. What is the difference between gauge and stat with gauge mode?
**Answer**: Gauge panel dedicated to showing single value with needle. Stat panel with gauge mode shows bar representation.

### 62. How do you implement compliance monitoring?
**Answer**: Track compliance-related metrics, alert on violations, create compliance reports.

### 63. What are SQL data source capabilities?
**Answer**: Query relational databases, use for custom metrics not in time-series databases.

### 64. How do you implement user behavior analytics?
**Answer**: Track user actions as custom metrics, analyze usage patterns, identify trends.

### 65. What is the difference between absolute and relative thresholds?
**Answer**: Absolute: fixed values. Relative: percentages. Used for different alerting scenarios.

### 66. How do you implement infrastructure monitoring?
**Answer**: Use node exporter for server metrics, container monitoring for Docker, VM metrics for cloud.

### 67. What are status history panels?
**Answer**: Show state changes over time (up/down, healthy/unhealthy) in timeline view.

### 68. How do you implement service dependency mapping?
**Answer**: Create dashboards showing service dependencies, track health of dependencies.

### 69. What is the difference between manual and automatic provisioning?
**Answer**: Manual: manually create dashboards in UI. Automatic: provisioning from configuration files.

### 70. How do you implement machine learning insights?
**Answer**: Use Grafana ML features or integrate external ML services for anomaly detection, forecasting.

### 71. What are public dashboards?
**Answer**: Dashboards accessible without authentication via public link. Useful for status pages.

### 72. How do you implement trend analysis?
**Answer**: Compare metrics over time periods, show trend arrows, calculate growth rates.

### 73. What is the difference between streaming and polling data?
**Answer**: Streaming: continuous data flow. Polling: periodic requests for data. Grafana primarily polls.

### 74. How do you implement incident tracking?
**Answer**: Create custom incident metrics, link to alerts, correlate with system behavior.

### 75. What are data links?
**Answer**: Links from dashboard values to other dashboards/URLs with context passed as variables.

### 76. How do you implement capacity planning?
**Answer**: Track historical usage trends, forecast future capacity needs, set up proactive alerts.

### 77. What is the difference between threshold and no data state?
**Answer**: Threshold: when to alert based on value. No data: when data is missing from query.

### 78. How do you implement Grafana plugins locally?
**Answer**: Clone plugin repo, build with yarn/npm, install in plugins folder, restart Grafana.

### 79. What are time series transformations?
**Answer**: Align times, fill gaps, resample, calculate derivatives for time series data.

### 80. How do you implement request tracing dashboards?
**Answer**: Integrate with tracing backend (Jaeger, Zipkin), visualize trace data alongside metrics.

### 81. What is the difference between dashboard refresh and query refresh?
**Answer**: Dashboard refresh: reload all panels. Query refresh: reload specific query data.

### 82. How do you implement log aggregation dashboards?
**Answer**: Use Loki data source, create log panels, correlate logs with metrics.

### 83. What are histogram panels?
**Answer**: Show distribution of values across buckets, useful for latency analysis.

### 84. How do you implement schema versioning?
**Answer**: Version dashboard definitions, track changes, implement rollback capability.

### 85. What is the difference between percentile and quantile?
**Answer**: Percentile: same as quantile. Both measure value at certain position in distribution.

### 86. How do you implement geographic monitoring?
**Answer**: Use geomaps to show metrics by location, track global service performance.

### 87. What are pie chart limitations?
**Answer**: Hard to compare sizes, limited to few segments. Use bar charts for better comparison.

### 88. How do you implement budget tracking?
**Answer**: Track spending metrics, set budget thresholds, alert on overspend.

### 89. What is the difference between row and series collapse?
**Answer**: Row collapse: hide/show entire row. Series collapse: hide/show specific series in graph.

### 90. How do you implement SLI dashboards?
**Answer**: Track Service Level Indicators, calculate SLO achievement, visualize trends.

### 91. What are options fields in Grafana?
**Answer**: Configuration options for panels like legend, tooltip, display format, thresholds.

### 92. How do you implement version comparison?
**Answer**: Tag deployments, compare metrics before/after deployment, measure impact.

### 93. What is the difference between timescaledb and prometheus?
**Answer**: TimescaleDB: scalable SQL database. Prometheus: dedicated metrics database. Different use cases.

### 94. How do you implement on-call dashboards?
**Answer**: Show current oncall schedules, recent incidents, alert status for quick handoff.

### 95. What are null handling options?
**Answer**: Skip, null as zero, zero, previous value. Controls how missing data is displayed.

### 96. How do you implement SLO tracking?
**Answer**: Define SLO, track actual performance, calculate error budget, alert on breaches.

### 97. What is the difference between local and cloud Grafana?
**Answer**: Local: self-hosted, full control, maintenance overhead. Cloud: managed by Grafana Labs, auto-updates.

### 98. How do you implement service quality monitoring?
**Answer**: Track quality metrics, alert on degradation, correlate with incidents.

### 99. How do you implement Grafana observability?
**Answer**: Monitor Grafana itself with metrics, track dashboard performance, user activity.

### 100. What are best practices for Grafana dashboards?
**Answer**:
- Clear, descriptive titles and descriptions
- Consistent color scheme and formatting
- Logical panel organization
- Use appropriate visualization types
- Set meaningful refresh intervals
- Implement dashboard variables for flexibility
- Document complex queries
- Version control dashboards
- Set up proper alerting
- Regular dashboard reviews and cleanup
- Monitor dashboard performance
- Test changes in staging
- Implement RBAC for security
- Use provisioning for consistency
- Archive unused dashboards

---

## Best Practices

### Dashboard Design
```javascript
// Good dashboard structure
{
  title: 'Application Health',
  description: 'Real-time application monitoring and performance metrics',
  tags: ['app', 'production', 'critical'],
  timezone: 'browser',
  panels: [
    // Overview metrics
    { title: 'Request Rate', type: 'stat' },
    { title: 'Error Rate', type: 'stat' },
    { title: 'P95 Latency', type: 'stat' },
    
    // Detailed trends
    { title: 'Request Rate Trend', type: 'graph' },
    { title: 'Error Rate Trend', type: 'graph' },
    
    // Service status
    { title: 'Service Status', type: 'table' }
  ]
}
```

### Query Optimization
```javascript
// Efficient query
rate(http_requests_total{job="api"}[5m])

// Avoid: High cardinality without filtering
{job=~".*"}  // Don't do this
```

### Alerting Best Practices
- Alert on symptoms, not causes
- Set meaningful thresholds based on data
- Include runbook links in alerts
- Set appropriate evaluation intervals
- Use alert grouping for related alerts
- Test alerts in staging before production

### Team & Organization
- Use organizations for multi-tenancy
- Set up RBAC properly
- Create team-specific dashboards
- Document dashboard purposes
- Archive unused dashboards regularly
- Implement change management

---

## Troubleshooting

### No Data in Dashboard
1. Verify data source connection
2. Check query syntax
3. Verify time range
4. Check metric availability
5. Review data source logs

### Slow Dashboards
1. Reduce number of panels
2. Increase refresh interval
3. Optimize queries
4. Use caching
5. Check data source performance

### Alert Not Firing
1. Verify alert rule condition
2. Check evaluation interval
3. Verify data source returning data
4. Check notification channel
5. Review alert logs

---

## Resources

- [Grafana Official Documentation](https://grafana.com/docs/)
- [Grafana API Documentation](https://grafana.com/docs/grafana/latest/fundamentals/auth-overview/api-overview/)
- [Prometheus Documentation](https://prometheus.io/docs/)
- [PromQL Basics](https://prometheus.io/docs/prometheus/latest/querying/basics/)
- [Grafana Plugins Directory](https://grafana.com/grafana/plugins/)
- [Grafana Cloud](https://grafana.com/products/cloud/)
- [Community Forums](https://community.grafana.com/)
- [GitHub Repository](https://github.com/grafana/grafana)
