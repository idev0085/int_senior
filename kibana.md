# Kibana - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Core Concepts & Architecture](#core-concepts--architecture)
2. [Data Visualization](#data-visualization)
3. [Dashboards & Canvas](#dashboards--canvas)
4. [Advanced Search & Queries](#advanced-search--queries)
5. [APM & Monitoring](#apm--monitoring)
6. [Security & Access Control](#security--access-control)

---

## Core Concepts & Architecture

### Q1: Explain the Elastic Stack (ELK) architecture and Kibana's role.

**Answer:**

**Simple Analogy:**
Think of the Elastic Stack like a library system:
- **Elasticsearch**: The bookshelves (stores all data)
- **Logstash**: The librarian who organizes incoming books
- **Kibana**: The search interface and reading room
- **Beats**: Couriers who deliver books from different sources

**Architecture:**

```
Data Sources
    ↓
Beats (Filebeat, Metricbeat, etc.)
    ↓
Logstash (Process & Transform)
    ↓
Elasticsearch (Store & Index)
    ↓
Kibana (Visualize & Analyze)
```

**Kibana's Role:**
- **Search Interface**: Query Elasticsearch data
- **Visualization**: Create charts, graphs, maps
- **Dashboards**: Combine visualizations
- **Management**: Configure Elasticsearch
- **Alerting**: Set up alerts on data
- **APM**: Monitor application performance
- **Security**: User management and RBAC

---

**Setup Example:**

**1. Elasticsearch (Storage):**
```yaml
# elasticsearch.yml
cluster.name: my-cluster
node.name: node-1
network.host: 0.0.0.0
discovery.type: single-node

# Security
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
```

**2. Logstash (Processing):**
```ruby
# logstash.conf
input {
  beats {
    port => 5044
  }
}

filter {
  grok {
    match => { "message" => "%{COMBINEDAPACHELOG}" }
  }
  date {
    match => [ "timestamp", "dd/MMM/yyyy:HH:mm:ss Z" ]
  }
  geoip {
    source => "clientip"
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "webserver-logs-%{+YYYY.MM.dd}"
    user => "elastic"
    password => "changeme"
  }
}
```

**3. Filebeat (Data Shipper):**
```yaml
# filebeat.yml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/nginx/*.log
  fields:
    service: nginx
    environment: production

output.logstash:
  hosts: ["localhost:5044"]

# or direct to Elasticsearch
output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme"
```

**4. Kibana (Visualization):**
```yaml
# kibana.yml
server.port: 5601
server.host: "0.0.0.0"
server.name: "my-kibana"

elasticsearch.hosts: ["http://localhost:9200"]
elasticsearch.username: "kibana_system"
elasticsearch.password: "changeme"

# Enable security
xpack.security.enabled: true
xpack.reporting.enabled: true
```

---

### Q2: What are Index Patterns and how do they work?

**Answer:**

**Simple Explanation:**
Index patterns are like filters that tell Kibana which Elasticsearch indices to search.

**Example:**

**Elasticsearch Indices:**
```
logs-webserver-2026.01.01
logs-webserver-2026.01.02
logs-webserver-2026.01.03
logs-database-2026.01.01
logs-database-2026.01.02
```

**Index Pattern:**
```
logs-webserver-*
```
This matches all webserver logs across all dates.

**Creating Index Pattern:**

**Via UI:**
```
Stack Management → Index Patterns → Create index pattern

1. Define pattern: logs-webserver-*
2. Select timestamp field: @timestamp
3. Create index pattern
```

**Via API:**
```bash
curl -X POST "localhost:5601/api/saved_objects/index-pattern/my-pattern" \
  -H 'kbn-xsrf: true' \
  -H 'Content-Type: application/json' \
  -d '{
    "attributes": {
      "title": "logs-webserver-*",
      "timeFieldName": "@timestamp"
    }
  }'
```

**Advanced Pattern:**
```
# Multiple indices
logs-*-2026.*

# Matches:
logs-webserver-2026.01.01
logs-database-2026.01.15
logs-application-2026.12.31
```

**Field Mapping:**
```json
{
  "@timestamp": "date",
  "message": "text",
  "status_code": "integer",
  "response_time": "float",
  "user_agent": "keyword",
  "ip_address": "ip"
}
```

---

## Data Visualization

### Q3: What are the different visualization types in Kibana and when should you use each?

**Answer:**

**1. Line Chart (Trends over time):**

**When to use:**
- Website traffic over days
- Server CPU usage trends
- Sales over months

**Example:**
```
Query: Show request count over time
X-axis: @timestamp (Date Histogram)
Y-axis: Count
```

**Configuration:**
```json
{
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "@timestamp",
        "interval": "1h"
      },
      "aggs": {
        "1": {
          "count": {}
        }
      }
    }
  }
}
```

---

**2. Bar Chart (Compare categories):**

**When to use:**
- Top 10 URLs by traffic
- Error counts by service
- Sales by product

**Example:**
```
Query: Top 10 pages by visits
X-axis: url.keyword (Terms aggregation, size: 10)
Y-axis: Count
```

---

**3. Pie Chart (Show proportions):**

**When to use:**
- Traffic by country
- Errors by type
- Browser distribution

**Example:**
```
Query: HTTP status code distribution
Slice: response.status_code (Terms)
Size: Count
```

---

**4. Data Table (Detailed view):**

**When to use:**
- Log entries
- Transaction details
- User activity

**Example:**
```
Columns:
- @timestamp
- source.ip
- url
- response_time
- status_code

Sort by: @timestamp desc
```

---

**5. Metric (Single value):**

**When to use:**
- Total requests today
- Average response time
- Current active users

**Example:**
```
Metric: Count of documents
Filter: @timestamp: last 24 hours
Font size: 60pt
Label: "Requests Today"
```

---

**6. Heatmap (Pattern detection):**

**When to use:**
- Traffic by hour and day of week
- Error patterns over time
- User activity patterns

**Example:**
```
X-axis: @timestamp (Date Histogram, hourly)
Y-axis: Day of week
Color intensity: Request count
```

---

**7. Gauge (Progress/Status):**

**When to use:**
- Disk usage percentage
- SLA compliance
- Performance score

**Example:**
```
Metric: Avg response time
Ranges:
  0-200ms: Green
  200-500ms: Yellow
  500+ms: Red
```

---

**8. Maps (Geo visualization):**

**When to use:**
- User locations
- Server locations
- Attack sources

**Example:**
```
Layer: Documents
Field: geoip.location
Clustering: true
Tooltip: Country, City, Request count
```

---

**9. TSVB (Time Series Visual Builder):**

**When to use:**
- Multiple metrics on one chart
- Complex calculations
- Annotations

**Example:**
```
Series 1: Request count (line)
Series 2: Error count (bar)
Series 3: Average response time (area)

Annotations: Deployment markers
```

---

**10. Vega/Vega-Lite (Custom visualizations):**

**When to use:**
- Custom requirements
- Complex visualizations not supported by default

**Example:**
```json
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "data": {
    "url": {
      "%context%": true,
      "index": "logs-*",
      "body": {
        "size": 0,
        "aggs": {
          "time_buckets": {
            "date_histogram": {
              "field": "@timestamp",
              "interval": "1h"
            }
          }
        }
      }
    },
    "format": {"property": "aggregations.time_buckets.buckets"}
  },
  "mark": "line",
  "encoding": {
    "x": {"field": "key", "type": "temporal"},
    "y": {"field": "doc_count", "type": "quantitative"}
  }
}
```

---

### Q4: How do you create effective dashboards in Kibana?

**Answer:**

**Dashboard Design Principles:**

**1. Top-to-Bottom, Left-to-Right:**
```
┌────────────────────────────────────┐
│  KPIs (Most Important)             │
├──────────────┬─────────────────────┤
│  Time Series │  Breakdown Charts   │
│  (Trends)    │  (Analysis)         │
├──────────────┴─────────────────────┤
│  Detailed Tables                   │
└────────────────────────────────────┘
```

**Example Dashboard Layout:**
```
Row 1: Key Metrics
┌─────────┬─────────┬─────────┬─────────┐
│ Total   │ Error   │ Avg     │ Active  │
│ Reqs    │ Rate    │ Latency │ Users   │
│ 1.2M    │ 0.5%    │ 234ms   │ 1,543   │
└─────────┴─────────┴─────────┴─────────┘

Row 2: Trends
┌────────────────────────────────────────┐
│ Requests Over Time (Line Chart)       │
│ [Chart showing hourly request trend]  │
└────────────────────────────────────────┘

Row 3: Analysis
┌──────────────────┬──────────────────────┐
│ Top URLs        │ Status Code Dist     │
│ (Bar Chart)     │ (Pie Chart)          │
└──────────────────┴──────────────────────┘

Row 4: Details
┌────────────────────────────────────────┐
│ Recent Errors (Data Table)            │
└────────────────────────────────────────┘
```

---

**2. Use Filters and Controls:**
```
[Time Picker: Last 24 hours ▼]
[Environment: Production ▼]
[Service: API ▼]

↓ Filters apply to all visualizations below
```

**Example:**
```json
{
  "query": {
    "bool": {
      "must": [
        { "match": { "environment": "production" } },
        { "match": { "service": "api" } }
      ],
      "filter": [
        { "range": { "@timestamp": { "gte": "now-24h" } } }
      ]
    }
  }
}
```

---

**3. Color Coding:**
```javascript
// Use consistent colors
Green: Success, healthy
Yellow: Warning, degraded
Red: Error, critical
Blue: Information
Gray: Inactive

// Example: Status code colors
200-299: Green
300-399: Blue
400-499: Yellow
500-599: Red
```

---

**4. Real-World Dashboard Examples:**

**A. Application Monitoring Dashboard:**
```
Title: Application Performance

Metrics:
- Request Rate: 1,234 req/min
- Error Rate: 0.5%
- P95 Latency: 234ms
- Apdex Score: 0.95

Charts:
- Request timeline (last 24 hours)
- Error breakdown by endpoint
- Latency distribution histogram
- Top slow endpoints

Filters:
- Environment
- Service
- Host
```

**B. Security Dashboard:**
```
Title: Security Monitoring

Metrics:
- Failed Logins: 45
- Blocked IPs: 12
- Active Threats: 3

Charts:
- Attack attempts over time
- Attacks by type
- Geographic source map
- Top attacked endpoints

Alerts:
- DDoS attempt detected
- Brute force login attempts
```

**C. Business Metrics Dashboard:**
```
Title: E-commerce Analytics

Metrics:
- Total Orders: 1,543
- Revenue: $45,678
- Conversion Rate: 3.2%
- Cart Abandonment: 68%

Charts:
- Sales over time
- Top products
- Sales by region (map)
- User journey funnel
```

---

**5. Dashboard Refresh & Auto-refresh:**
```json
{
  "refreshInterval": {
    "pause": false,
    "value": 30000  // 30 seconds
  },
  "time": {
    "from": "now-15m",
    "to": "now"
  }
}
```

---

## Advanced Search & Queries

### Q5: Explain KQL (Kibana Query Language) and Lucene query syntax. When should you use each?

**Answer:**

**KQL (Kibana Query Language) - Simple, User-Friendly:**

**Basic Syntax:**
```
# Exact match
status:200

# Multiple values (OR)
status:(200 or 404)

# Range
response_time > 1000

# Wildcard
url:"/api/*"

# AND conditions
status:200 and response_time > 500

# NOT
not status:404

# Exists
url:*

# Nested fields
user.name:"John"
```

**Examples:**
```
# Find errors in production
environment:production and level:error

# Slow API requests
url:"/api/*" and response_time > 2000

# Failed logins from specific IP
action:login and status:failed and source.ip:"192.168.1.100"

# High memory usage
system.memory.used.pct > 0.9
```

---

**Lucene Query Syntax - Powerful, Complex:**

**Basic Syntax:**
```
# Exact match
status:200

# Phrase match
message:"user logged in"

# Wildcard
url:/api/*

# Regex
url:/api\/user\/[0-9]+/

# Boolean operators (AND, OR, NOT)
status:200 AND response_time:[500 TO *]

# Range
response_time:[100 TO 500]
response_time:{100 TO 500}  # Exclusive

# Boost relevance
priority:high^2 OR priority:medium
```

**Advanced Examples:**
```
# Regex for email validation
email:/.*@example\.com/

# Fuzzy search (typos)
name:john~

# Proximity search (words within 5 positions)
message:"error database"~5

# Field exists
_exists_:error_message

# Complex boolean
(status:500 OR status:502) AND NOT url:"/health" AND response_time:[1000 TO *]
```

---

**When to Use Each:**

| Feature | KQL | Lucene |
|---------|-----|--------|
| Ease of use | ✅ Easy | ❌ Complex |
| Autocomplete | ✅ Yes | ❌ No |
| Regex | ❌ No | ✅ Yes |
| Fuzzy search | ❌ No | ✅ Yes |
| Nested queries | ✅ Better | ❌ Harder |
| Wildcards | ✅ Simple | ✅ Advanced |
| **Use for** | **Daily searches** | **Power users** |

---

**Query DSL (Full Power):**

For complex queries, use Elasticsearch Query DSL:

```json
POST /logs-*/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "message": "error" } },
        { "range": { "@timestamp": { "gte": "now-1h" } } }
      ],
      "should": [
        { "term": { "level": "error" } },
        { "term": { "level": "fatal" } }
      ],
      "must_not": [
        { "term": { "environment": "test" } }
      ],
      "filter": [
        { "term": { "service": "api" } }
      ]
    }
  },
  "aggs": {
    "errors_over_time": {
      "date_histogram": {
        "field": "@timestamp",
        "interval": "5m"
      }
    },
    "top_errors": {
      "terms": {
        "field": "error_type.keyword",
        "size": 10
      }
    }
  }
}
```

---

## APM & Monitoring

### Q6: How do you set up Application Performance Monitoring (APM) with Kibana?

**Answer:**

**Architecture:**
```
Application (instrumented)
    ↓
APM Agent (in-process)
    ↓
APM Server
    ↓
Elasticsearch
    ↓
Kibana APM UI
```

---

**1. Install APM Server:**

```yaml
# apm-server.yml
apm-server:
  host: "0.0.0.0:8200"
  rum:
    enabled: true
    allow_origins: ["*"]

output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "apm_system"
  password: "changeme"

kibana:
  enabled: true
  host: "localhost:5601"
```

---

**2. Instrument Application:**

**Node.js:**
```javascript
// app.js (first line!)
const apm = require('elastic-apm-node').start({
  serviceName: 'my-api',
  secretToken: 'your-secret-token',
  serverUrl: 'http://localhost:8200',
  environment: process.env.NODE_ENV || 'development',
  logLevel: 'info'
});

const express = require('express');
const app = express();

// Your application code
app.get('/api/users', async (req, res) => {
  // APM automatically tracks this request
  
  // Custom transaction
  const span = apm.startSpan('Database Query');
  const users = await db.query('SELECT * FROM users');
  span.end();
  
  res.json(users);
});

// Error tracking
app.use((err, req, res, next) => {
  apm.captureError(err);
  res.status(500).json({ error: err.message });
});
```

---

**Java (Spring Boot):**
```xml
<!-- pom.xml -->
<dependency>
  <groupId>co.elastic.apm</groupId>
  <artifactId>apm-agent-api</artifactId>
  <version>1.36.0</version>
</dependency>
```

```bash
# Run with agent
java -javaagent:/path/to/elastic-apm-agent.jar \
  -Delastic.apm.service_name=my-service \
  -Delastic.apm.server_url=http://localhost:8200 \
  -Delastic.apm.application_packages=com.mycompany \
  -jar myapp.jar
```

---

**Python (Flask):**
```python
from elasticapm.contrib.flask import ElasticAPM

app = Flask(__name__)
app.config['ELASTIC_APM'] = {
    'SERVICE_NAME': 'my-flask-app',
    'SERVER_URL': 'http://localhost:8200',
    'SECRET_TOKEN': 'your-secret-token',
    'ENVIRONMENT': 'production'
}
apm = ElasticAPM(app)

@app.route('/api/data')
def get_data():
    # Automatically tracked
    return jsonify(data)

# Custom spans
from elasticapm import capture_span

@capture_span()
def slow_function():
    # This function is tracked as a span
    time.sleep(2)
```

---

**3. Frontend (RUM - Real User Monitoring):**

```javascript
// Initialize Elastic APM RUM
import { init as initApm } from '@elastic/apm-rum';

const apm = initApm({
  serviceName: 'my-frontend',
  serverUrl: 'http://localhost:8200',
  serviceVersion: '1.0.0',
  environment: 'production'
});

// Track page loads automatically
// Track route changes (React Router)
import { ApmRoute } from '@elastic/apm-rum-react';

<ApmRoute path="/users" component={UserList} />

// Custom transactions
const transaction = apm.startTransaction('checkout-process', 'custom');
// ... checkout code
transaction.end();

// Error tracking
try {
  throw new Error('Something went wrong');
} catch (e) {
  apm.captureError(e);
}
```

---

**4. Kibana APM UI Features:**

**Services Overview:**
```
Services List:
- my-api: 1,234 req/min, 250ms avg
- payment-service: 567 req/min, 150ms avg
- user-service: 890 req/min, 100ms avg
```

**Transaction Details:**
```
Transaction: GET /api/users
Duration: 234ms
Breakdown:
- Application: 20ms (8%)
- Database: 200ms (85%)
- External: 14ms (6%)

Trace:
1. GET /api/users (234ms)
   ├─ SELECT FROM users (180ms)
   ├─ SELECT FROM permissions (20ms)
   └─ Redis cache check (14ms)
```

**Service Map:**
```
Frontend
   ↓
API Gateway
   ├─ User Service → PostgreSQL
   ├─ Payment Service → MySQL
   │                  → Stripe API
   └─ Notification Service → RabbitMQ
```

**Error Tracking:**
```
Error: Database connection timeout
Occurrences: 45 (last hour)
First seen: 2026-01-20 10:15:23
Last seen: 2026-01-20 11:42:15

Stack trace:
  at DatabasePool.connect (db.js:45)
  at UserService.getUsers (user-service.js:23)
  at APIController.handleRequest (api.js:67)

Affected transactions:
- GET /api/users: 35 errors
- POST /api/users: 10 errors
```

---

**5. Alerting:**

```json
// Create alert for slow transactions
{
  "name": "Slow API Response",
  "conditions": {
    "transaction.duration.us": {
      "threshold": 2000000,  // 2 seconds
      "aggregationType": "avg"
    }
  },
  "actions": [
    {
      "type": "slack",
      "channel": "#alerts",
      "message": "API response time > 2s"
    }
  ]
}
```

---

## Security & Access Control

### Q7: How do you implement security and access control in Kibana?

**Answer:**

**1. Authentication Methods:**

**A. Basic Authentication:**
```yaml
# elasticsearch.yml
xpack.security.enabled: true

# Create users
bin/elasticsearch-users useradd admin -p password -r superuser
bin/elasticsearch-users useradd developer -p devpass -r kibana_user
```

---

**B. LDAP/Active Directory:**
```yaml
# elasticsearch.yml
xpack.security.authc.realms.ldap.ldap1:
  order: 0
  url: "ldap://ldap.company.com:389"
  bind_dn: "cn=admin,dc=company,dc=com"
  bind_password: "password"
  user_search:
    base_dn: "ou=users,dc=company,dc=com"
    filter: "(cn={0})"
  group_search:
    base_dn: "ou=groups,dc=company,dc=com"
  role_mapping:
    admin: "cn=admins,ou=groups,dc=company,dc=com"
    developer: "cn=developers,ou=groups,dc=company,dc=com"
```

---

**C. SAML (Single Sign-On):**
```yaml
# kibana.yml
xpack.security.authc.providers:
  saml.saml1:
    order: 0
    realm: saml1
  basic.basic1:
    order: 1
    enabled: false
```

---

**2. Role-Based Access Control (RBAC):**

**Define Roles:**
```json
POST /_security/role/log_viewer
{
  "cluster": ["monitor"],
  "indices": [
    {
      "names": ["logs-*"],
      "privileges": ["read", "view_index_metadata"]
    }
  ],
  "applications": [
    {
      "application": "kibana-.kibana",
      "privileges": ["read"],
      "resources": ["*"]
    }
  ]
}

POST /_security/role/developer
{
  "cluster": ["monitor", "manage_index_templates"],
  "indices": [
    {
      "names": ["logs-*", "metrics-*"],
      "privileges": ["read", "write", "create_index", "view_index_metadata"]
    }
  ],
  "applications": [
    {
      "application": "kibana-.kibana",
      "privileges": ["all"],
      "resources": ["space:development"]
    }
  ]
}

POST /_security/role/admin
{
  "cluster": ["all"],
  "indices": [
    {
      "names": ["*"],
      "privileges": ["all"]
    }
  ],
  "applications": [
    {
      "application": "kibana-.kibana",
      "privileges": ["all"],
      "resources": ["*"]
    }
  ]
}
```

**Assign Roles to Users:**
```json
POST /_security/user/john
{
  "password": "password123",
  "roles": ["developer"],
  "full_name": "John Doe",
  "email": "john@company.com"
}
```

---

**3. Space-Based Access:**

```
Kibana Spaces:
├── Production (only admins)
├── Development (developers + admins)
└── Testing (everyone)
```

**Create Space:**
```json
POST /api/spaces/space
{
  "id": "development",
  "name": "Development",
  "description": "Development environment",
  "disabledFeatures": ["ml"],  // Disable machine learning
  "imageUrl": "/path/to/image.png"
}
```

**Space-Specific Role:**
```json
POST /_security/role/dev_space_user
{
  "applications": [
    {
      "application": "kibana-.kibana",
      "privileges": ["all"],
      "resources": ["space:development"]
    }
  ]
}
```

---

**4. Document-Level Security (DLS):**

```json
POST /_security/role/us_logs_only
{
  "indices": [
    {
      "names": ["logs-*"],
      "privileges": ["read"],
      "query": {
        "term": { "country": "US" }
      }
    }
  ]
}

// User with this role only sees US logs
```

---

**5. Field-Level Security (FLS):**

```json
POST /_security/role/pii_restricted
{
  "indices": [
    {
      "names": ["users-*"],
      "privileges": ["read"],
      "field_security": {
        "grant": ["*"],
        "except": ["ssn", "credit_card", "password"]
      }
    }
  ]
}

// User sees all fields except sensitive ones
```

---

**6. API Key Authentication:**

```bash
# Create API key
POST /_security/api_key
{
  "name": "monitoring-key",
  "role_descriptors": {
    "log_reader": {
      "indices": [
        {
          "names": ["logs-*"],
          "privileges": ["read"]
        }
      ]
    }
  },
  "expiration": "7d"
}

# Response
{
  "id": "VuaCfGcBCdbkQm-e5aOx",
  "name": "monitoring-key",
  "api_key": "ui2lp2axTNmsyakw9tvNnw"
}

# Use API key
curl -H "Authorization: ApiKey VuaCfGcBCdbkQm-e5aOx:ui2lp2axTNmsyakw9tvNnw" \
  http://localhost:9200/_search
```

---

**7. Audit Logging:**

```yaml
# elasticsearch.yml
xpack.security.audit.enabled: true
xpack.security.audit.logfile.events.include:
  - access_granted
  - access_denied
  - authentication_failed
  - authentication_success

# Log file: logs/my-cluster_audit.json
```

**Example Audit Log:**
```json
{
  "type": "audit",
  "timestamp": "2026-01-20T10:15:30.123Z",
  "event.action": "authentication_success",
  "user.name": "john",
  "source.ip": "192.168.1.100",
  "url.path": "/api/saved_objects"
}
```

---

## Summary

**Kibana Best Practices:**

1. **Index Patterns**: Use clear naming conventions (logs-*, metrics-*)
2. **Visualizations**: Choose appropriate chart types
3. **Dashboards**: Top-to-bottom, most important first
4. **Queries**: KQL for simplicity, Lucene for power
5. **APM**: Instrument critical services
6. **Security**: Implement RBAC and spaces
7. **Performance**: Use index lifecycle management
8. **Alerting**: Set up meaningful alerts
9. **Backup**: Regular snapshots
10. **Monitoring**: Monitor Elastic Stack itself

**Remember:** Kibana is only as good as the data you put in. Focus on structured logging and meaningful metrics!
