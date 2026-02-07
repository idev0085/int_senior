# Front-End Monitoring & Observability Tools - Complete Guide

## Table of Contents
1. [Fundamentals](#fundamentals)
2. [Key Concepts & Metrics](#key-concepts--metrics)
3. [New Relic](#new-relic)
4. [Other Monitoring Tools](#other-monitoring-tools)
5. [Implementation Guide](#implementation-guide)
6. [Interview Q&A](#interview-qa)

---

## Fundamentals

### What is Front-End Monitoring?
Front-end monitoring tracks user experience, application performance, and issues that occur in the client-side of web applications. It helps identify problems before users report them.

### What is Observability?
Observability is the ability to understand the internal state of a system by examining its outputs (logs, metrics, traces). In front-end: understanding user behavior, performance, and errors.

### Why is Front-End Monitoring Important?
- **User Experience**: Detect issues affecting users in real-time
- **Performance**: Identify slow pages and bottlenecks
- **Error Tracking**: Catch exceptions and errors early
- **Analytics**: Understand user behavior and patterns
- **Business Impact**: Correlate technical issues with business metrics

### Key Benefits
- Faster issue detection and resolution
- Improved user satisfaction
- Data-driven optimization
- Reduced mean time to resolution (MTTR)
- Better product decisions

---

## Key Concepts & Metrics

### Core Metrics

#### 1. Performance Metrics
**First Contentful Paint (FCP)**
- Time until first content renders
- Target: < 1.8 seconds

**Largest Contentful Paint (LCP)**
- Time until largest content element renders
- Target: < 2.5 seconds

**Cumulative Layout Shift (CLS)**
- Measure of visual stability
- Target: < 0.1

**First Input Delay (FID)**
- Time from user input to response
- Target: < 100ms
- Being replaced by Interaction to Next Paint (INP)

**Time to First Byte (TTFB)**
- Time until first byte of response received
- Target: < 600ms

#### 2. Error Metrics
```javascript
// Tracking error rates
{
  errorCount: 150,
  errorRate: 0.5, // 0.5 errors per 1000 pageviews
  criticalErrors: 10,
  sessionAffected: 45 // sessions with errors
}
```

#### 3. User Experience Metrics
```javascript
{
  sessionDuration: 480, // seconds
  pageViewsPerSession: 3.5,
  bounceRate: 0.25,
  conversionRate: 0.08,
  frustrationSignals: {
    ragClicks: 12,
    deadClicks: 5,
    errorClicks: 3
  }
}
```

#### 4. Network Metrics
```javascript
{
  domContentLoaded: 1200, // ms
  pageLoadTime: 2500, // ms
  resourceCount: 45,
  imageCount: 28,
  jsSize: 450, // KB
  cssSize: 120 // KB
}
```

### Web Vitals
Google's Core Web Vitals measure user experience:
- **LCP**: Loading performance
- **FID/INP**: Interactivity
- **CLS**: Visual stability

---

## New Relic

### Overview
New Relic is a cloud-based observability platform providing real-time insights into application performance and user experience.

### Key Features
- **Real User Monitoring (RUM)**: Tracks actual user interactions
- **Distributed Tracing**: Follow requests across services
- **Application Performance Monitoring (APM)**: Detailed transaction analysis
- **Infrastructure Monitoring**: Server and network monitoring
- **Error Analytics**: Exception tracking and analysis
- **Custom Events**: Create custom metrics and events

### Installation

#### Browser Agent Setup
```javascript
// Via copy-paste method in <head>
<script type="text/javascript">
  window.NREUM||(NREUM={});
  NREUM.info={
    beacon:"bam.nr-data.net",
    errorBeacon:"bam.nr-data.net",
    licenseKey:"YOUR_KEY",
    applicationID:"YOUR_ID",
    sa:1
  };
  
  // Load agent
  if(navigator.userAgent.match(/iPad|iPhone|iPod|Android/)){
    document.write('<script src="https://js-agent.newrelic.com/..."></script>')
  }
</script>
```

#### NPM Installation
```bash
npm install @newrelic/browser-agent
```

```javascript
// Initialize in your app
import { agent } from '@newrelic/browser-agent'

agent.start({
  licenseKey: 'YOUR_KEY',
  applicationID: 'YOUR_ID'
})
```

### Key Metrics Dashboard

**Page Load Metrics**
- Page views
- Average page load time
- First contentful paint
- Largest contentful paint

**Error Tracking**
- Error rate by page
- Top errors
- Error trends
- Browser breakdown

**Custom Events**
```javascript
// Track custom events
newrelic.addPageAction("UserSignUp", {
  userId: 123,
  accountType: "premium"
});

// Monitor custom metrics
newrelic.recordMetric('Custom/MetricName', 1000);
```

### Transaction Analysis
```javascript
// Monitor specific operations
newrelic.noticeError(error);

// Mark feature usage
newrelic.addPageAction('FeatureUsed', {
  feature: 'search',
  resultsCount: 42
});
```

### Alerting Configuration
```javascript
// Setup alerts for thresholds
{
  metric: 'Frontend/RUM/PageLoadTime',
  operator: 'above',
  threshold: 5000, // ms
  duration: 5 // minutes
}
```

### Real User Monitoring (RUM)
Tracks actual users using your application:
- Page views and navigation
- JavaScript errors
- Resource loading
- User interactions
- Session replay (optional)

### Synthetic Monitoring
Automated tests simulating user interactions:
```javascript
{
  name: "Checkout Flow",
  locations: ["US-East", "EU-West"],
  frequency: 5, // minutes
  steps: [
    { action: "go", target: "https://example.com" },
    { action: "click", target: "button.add-to-cart" },
    { action: "fillIn", target: "input[name='email']", value: "test@example.com" }
  ]
}
```

### NRQL (New Relic Query Language)
Query platform data:
```sql
-- Page load times by page
SELECT average(duration) FROM PageView WHERE appName='MyApp' FACET name

-- Error rate over time
SELECT count(*) FROM JavaScriptError SINCE 1 hour ago TIMESERIES

-- Top pages by traffic
SELECT count(*) FROM PageView WHERE appName='MyApp' FACET name LIMIT 10

-- Resource load times
SELECT average(duration) FROM Resource FACET host
```

### Session Replay
Records user sessions for debugging:
```javascript
// Enable session replay
{
  session_trace: {
    enabled: true,
    sampling_rate: 0.1 // 10% of sessions
  }
}
```

---

## Other Monitoring Tools

### Sentry
Error tracking and performance monitoring.

**Features**:
- Exception tracking
- Release tracking
- Performance monitoring
- Distributed tracing
- Session replay

```javascript
import * as Sentry from "@sentry/react";

Sentry.init({
  dsn: "YOUR_DSN",
  tracesSampleRate: 1.0,
  integrations: [
    new Sentry.Replay({
      maskAllText: true,
      blockAllMedia: true,
    }),
  ],
});

// Capture exceptions
try {
  riskyOperation();
} catch (error) {
  Sentry.captureException(error);
}

// Track custom messages
Sentry.captureMessage("User signed up", "info");
```

### Datadog
Cloud monitoring for infrastructure and applications.

**Features**:
- Real User Monitoring (RUM)
- Synthetic monitoring
- Application Performance Monitoring (APM)
- Infrastructure monitoring
- Log Management

```javascript
import { datadogRum } from '@datadog/browser-rum';

datadogRum.init({
  applicationId: 'YOUR_APP_ID',
  clientToken: 'YOUR_CLIENT_TOKEN',
  site: 'datadoghq.com',
  service: 'my-web-app',
  env: 'production',
  version: '1.0.0',
  sessionSampleRate: 100,
  sessionReplaySampleRate: 20,
  trackUserInteractions: true,
  trackResources: true,
  trackLongTasks: true,
  defaultPrivacyLevel: 'mask-user-input',
});

datadogRum.startSessionReplayRecording();
```

### LogRocket
Session replay and error tracking focused on frontend.

**Features**:
- Session replay
- Error tracking
- Network monitoring
- Console logs
- Redux/Vuex state

```javascript
import LogRocket from 'logrocket';

LogRocket.init('YOUR_APP_ID');

// Identify user
LogRocket.identify(userId, {
  name: "User Name",
  email: "user@example.com",
  subscriptionType: "premium"
});

// Track custom events
LogRocket.track('Feature Used', {
  feature: 'search'
});
```

### Google Analytics 4
Web analytics platform with Core Web Vitals support.

**Features**:
- User behavior tracking
- Event tracking
- Conversion tracking
- Web Vitals reporting
- Custom dashboards

```javascript
// Track Core Web Vitals
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';

getCLS(metric => gtag('event', 'CLS', { value: metric.value }));
getFID(metric => gtag('event', 'FID', { value: metric.value }));
getFCP(metric => gtag('event', 'FCP', { value: metric.value }));
getLCP(metric => gtag('event', 'LCP', { value: metric.value }));
getTTFB(metric => gtag('event', 'TTFB', { value: metric.value }));

// Custom events
gtag('event', 'user_signup', {
  method: 'google'
});
```

### Kibana & Elasticsearch
Open-source monitoring and visualization.

**Features**:
- Log aggregation
- Metrics visualization
- Real-time monitoring
- Alerting
- Custom dashboards

### Grafana
Open-source monitoring and visualization platform.

**Features**:
- Time-series visualization
- Alerting
- Data source integration
- Custom dashboards
- Open-source and cloud options

### Lighthouse CI
Automated performance testing in CI/CD.

```javascript
// lighthouse.config.js
module.exports = {
  ci: {
    collect: {
      url: ['http://localhost:3000'],
      numberOfRuns: 3,
    },
    upload: {
      target: 'lhci',
      serverBaseUrl: 'https://lhci.example.com',
    },
    assert: {
      preset: 'lighthouse:recommended',
      assertions: {
        'categories:performance': ['error', { minScore: 0.8 }],
        'categories:accessibility': ['error', { minScore: 0.9 }],
      },
    },
  },
};
```

### Elastic APM
Application performance monitoring.

**Features**:
- Transaction tracking
- Error tracking
- Service mapping
- Distributed tracing

### Dynatrace
Enterprise application monitoring.

**Features**:
- Real User Monitoring
- Application Performance Monitoring
- Infrastructure monitoring
- AI-powered insights
- Automated root cause analysis

---

## Implementation Guide

### Step 1: Define Requirements
- What metrics matter for your business?
- What are your SLOs/SLAs?
- Budget constraints?
- Team expertise?

### Step 2: Choose Tool
Consider:
- Features needed
- Integration capabilities
- Pricing model
- Support and documentation
- Team knowledge

### Step 3: Installation
```javascript
// Example: Install New Relic
npm install @newrelic/browser-agent

// Initialize early in application
import { agent } from '@newrelic/browser-agent'

agent.start({
  licenseKey: process.env.REACT_APP_NEW_RELIC_KEY,
  applicationID: process.env.REACT_APP_NEW_RELIC_APP_ID
})
```

### Step 4: Configure Dashboards
- Set up performance dashboard
- Error tracking dashboard
- Custom business metrics dashboard
- Real-time alerts dashboard

### Step 5: Set Alerts
```javascript
{
  alerts: [
    {
      name: "High Error Rate",
      metric: "errorRate",
      threshold: 0.5, // 0.5 errors per 1000 pageviews
      duration: 5,
      notification: "slack"
    },
    {
      name: "Slow Page Load",
      metric: "pageLoadTime",
      threshold: 5000, // ms
      duration: 10,
      notification: "email"
    },
    {
      name: "High CLS",
      metric: "cls",
      threshold: 0.25,
      duration: 5,
      notification: "pagerduty"
    }
  ]
}
```

### Step 6: Custom Instrumentation
```javascript
// Track specific user actions
function trackUserAction(action, details) {
  newrelic.addPageAction(action, details);
  // or
  Sentry.captureMessage(`User action: ${action}`, 'info');
  // or
  datadogRum.addUserAction(action, details);
}

// Track performance-critical operations
const startTime = performance.now();
expensiveOperation();
const duration = performance.now() - startTime;
newrelic.recordMetric('Custom/OperationDuration', duration);
```

### Step 7: Error Handling Integration
```javascript
// Setup global error handler
window.addEventListener('error', (event) => {
  Sentry.captureException(event.error);
  LogRocket.captureException(event.error);
  newrelic.noticeError(event.error);
});

// Unhandled promise rejection
window.addEventListener('unhandledrejection', (event) => {
  Sentry.captureException(event.reason);
});
```

### Step 8: Source Maps
```javascript
// Upload source maps for better error tracking
// For Sentry
curl https://files.example.com/releases/1.0.0 \
  -F upload=@sourcemap.js.map

// For New Relic
newrelic.setBrowserAttributes({ 
  version: '1.0.0',
  environment: 'production'
});
```

### Step 9: Testing & Validation
```javascript
// Test error tracking
throw new Error("Test error");

// Test custom events
newrelic.addPageAction("TestAction", { test: true });

// Test performance tracking
setTimeout(() => {
  newrelic.recordMetric('Custom/TestMetric', 1000);
}, 100);
```

### Step 10: Documentation & Training
- Document monitoring setup
- Create runbooks for alerts
- Train team on dashboards
- Establish incident response procedures

---

## Interview Q&A

### 1. What is front-end monitoring?
**Answer**: Front-end monitoring involves tracking user experience, application performance, and errors occurring on the client-side. It helps identify issues affecting users and gather insights into how the application performs in real-world conditions.

### 2. What is the difference between monitoring and observability?
**Answer**: Monitoring checks if a system is working (yes/no). Observability is the ability to understand internal state from outputs. Observability is broader and includes monitoring, logging, tracing, and metrics.

### 3. What are Core Web Vitals?
**Answer**: Google's Core Web Vitals are three metrics measuring user experience:
- **LCP** (Largest Contentful Paint): Loading performance
- **FID/INP** (First Input Delay/Interaction to Next Paint): Responsiveness
- **CLS** (Cumulative Layout Shift): Visual stability

### 4. What is the difference between Real User Monitoring and Synthetic Monitoring?
**Answer**: 
- **RUM**: Tracks actual users using the application in production
- **Synthetic Monitoring**: Automated scripts simulating user interactions from different locations

### 5. What is New Relic?
**Answer**: New Relic is a cloud-based observability platform providing real-time insights into application performance, user experience, and infrastructure. It includes APM, RUM, error tracking, and distributed tracing.

### 6. How do you implement New Relic in a web application?
**Answer**: Install via NPM or copy-paste method in HTML head, initialize with license key and application ID, then the agent automatically tracks page views, errors, and performance metrics.

### 7. What is NRQL?
**Answer**: New Relic Query Language is used to query observability data. It allows you to analyze metrics, events, logs, and traces to get insights about your application.

### 8. What metrics should you track for front-end monitoring?
**Answer**: 
- Performance: FCP, LCP, CLS, TTFB, page load time
- Errors: Error rate, error count, top errors
- User behavior: Page views, session duration, bounce rate
- Resources: Image count, JS size, CSS size, network requests

### 9. What is session replay?
**Answer**: Session replay records user sessions allowing you to playback exactly what the user experienced. Useful for debugging issues and understanding user behavior.

### 10. How do you track custom events in New Relic?
**Answer**:
```javascript
newrelic.addPageAction("CustomEventName", {
  key1: "value1",
  key2: "value2"
});
```

### 11. What is distributed tracing?
**Answer**: Distributed tracing follows a request across multiple services and systems, showing where time is spent and where failures occur. Essential for microservices architectures.

### 12. What is the difference between New Relic and Sentry?
**Answer**: New Relic is a full observability platform including APM, RUM, and infrastructure monitoring. Sentry focuses primarily on error tracking and performance monitoring with session replay.

### 13. What is the difference between New Relic and Datadog?
**Answer**: Both are full observability platforms. Datadog is more infrastructure-focused, New Relic emphasizes application performance and user experience. Pricing models differ.

### 14. How do you set up alerts in New Relic?
**Answer**: Create alert policies defining conditions (metric, threshold, duration), notification channels (email, slack, PagerDuty), and severity levels.

### 15. What is First Contentful Paint (FCP)?
**Answer**: FCP measures the time until the first content appears on screen. Target is under 1.8 seconds. It indicates when the page starts becoming visible to the user.

### 16. What is Largest Contentful Paint (LCP)?
**Answer**: LCP measures when the largest visible element on the page becomes visible. Target is under 2.5 seconds. Better indicator of page usability than FCP.

### 17. What is Cumulative Layout Shift (CLS)?
**Answer**: CLS measures unexpected visual shifts during page load. Target is under 0.1. A high CLS frustrates users by moving elements around.

### 18. How do you optimize for LCP?
**Answer**: 
- Optimize server response time
- Reduce JavaScript execution time
- Optimize images and fonts
- Use lazy loading
- Implement code splitting

### 19. How do you measure JavaScript execution time?
**Answer**:
```javascript
const startTime = performance.now();
expensiveFunction();
const endTime = performance.now();
console.log(`Execution time: ${endTime - startTime}ms`);
```

### 20. What is the Performance API?
**Answer**: Browser API providing granular performance measurements including page load, resource timing, user timing, and navigation timing.

### 21. How do you identify performance bottlenecks?
**Answer**: Use browser DevTools, Lighthouse, monitoring dashboards, and analyze slow transactions in APM tools. Look for longest-running code paths.

### 22. What is rate limiting in monitoring?
**Answer**: Rate limiting prevents the monitoring agent from sending too many requests, managing costs and network usage while maintaining data quality.

### 23. What is source map uploading?
**Answer**: Source maps map minified/transpiled code back to original source. Uploading them to monitoring tools enables better error reporting with actual line numbers.

### 24. How do you track user identity in monitoring?
**Answer**: Set user context/identity in monitoring tools to correlate errors and behavior with specific users:
```javascript
newrelic.setUser({ id: userId, name: userName, email: userEmail });
Sentry.setUser({ id: userId, email: userEmail });
```

### 25. What is error budgeting?
**Answer**: Error budgets define acceptable error rates for services. Based on SLO, you can "spend" errors during development, but must maintain SLO in production.

### 26. What is the difference between SLA and SLO?
**Answer**: 
- **SLO** (Service Level Objective): Internal target for service performance
- **SLA** (Service Level Agreement): Contractual commitment with consequences for missing SLO

### 27. How do you debug intermittent issues with monitoring?
**Answer**: Use session replay, error context, user journey tracking, and correlate with network conditions, device types, and browser versions.

### 28. What is resource timing?
**Answer**: Measurements of how long it takes to load various resources (images, stylesheets, scripts, fonts) providing insight into network and server performance.

### 29. How do you track API performance?
**Answer**: Monitor API response times, error rates, and payloads. Track AJAX calls in monitoring tools:
```javascript
// Automatically tracked by most monitoring tools
fetch('/api/users')
  .then(response => response.json())
  .catch(error => Sentry.captureException(error));
```

### 30. What is the impact of monitoring on performance?
**Answer**: Well-implemented monitoring has minimal impact (< 1-2% overhead). Key: sample selectively, compress data, defer non-critical work, and use service workers.

### 31. How do you handle personally identifiable information (PII) in monitoring?
**Answer**: Mask or redact sensitive data. Most tools provide config for sanitizing PII:
```javascript
{
  maskAllText: true,
  blockAllMedia: true,
  defaultPrivacyLevel: 'mask-user-input'
}
```

### 32. What is breadcrumb tracking?
**Answer**: Recording events (clicks, navigation, errors) leading up to an error for better debugging context.

### 33. How do you monitor Single Page Applications (SPAs)?
**Answer**: Track route changes and virtual page views, monitor API calls, track SPA-specific metrics like time to interactive, use monitoring tools with SPA support.

### 34. What is the difference between logs, metrics, and traces?
**Answer**: 
- **Logs**: Discrete text records of events
- **Metrics**: Numerical measurements over time
- **Traces**: Request flow across systems

### 35. How do you create custom metrics?
**Answer**:
```javascript
// New Relic
newrelic.recordMetric('Custom/MyMetric', value);

// Sentry
Sentry.captureMessage('Custom metric', 'info', { extra: { metric: value } });

// Datadog
datadogRum.recordMetric('custom.metric', value);
```

### 36. What is the difference between sampling and rate limiting?
**Answer**: 
- **Sampling**: Collecting data from subset of traffic (e.g., 10%)
- **Rate limiting**: Capping total requests sent to monitoring backend

### 37. How do you monitor performance in different network conditions?
**Answer**: Use synthetic monitoring with throttling, segment RUM data by network type (4G, 3G, WiFi), and focus on actual user metrics.

### 38. What is time to interactive (TTI)?
**Answer**: Time until page is fully loaded and responsive to user input. Not an official metric but important for UX understanding.

### 39. How do you track conversion funnels with monitoring?
**Answer**: Track specific user actions through funnel steps and correlate with performance metrics:
```javascript
newrelic.addPageAction('FunnelStep', { 
  step: 'cart',
  value: 299.99 
});
```

### 40. What is real-time alerting?
**Answer**: Alerts triggered immediately when thresholds are violated, enabling fast response to critical issues.

### 41. How do you calculate error rate?
**Answer**: Error Rate = (Number of Errors / Total Requests) × 100 or expressed as errors per 1000 pageviews.

### 42. What is a SLO violation?
**Answer**: When actual service performance falls below the defined Service Level Objective, triggering incident response.

### 43. How do you monitor third-party script impact?
**Answer**: Track third-party script load times and errors separately, monitor performance impact, and evaluate necessity of each script.

### 44. What is field data vs lab data?
**Answer**: 
- **Field data (RUM)**: Real users, real conditions, real experiences
- **Lab data**: Controlled testing environment (Lighthouse, synthetic)

### 45. How do you optimize Cumulative Layout Shift?
**Answer**: Reserve space for elements, avoid injecting content above existing content, use transform animations instead of layout changes, avoid web fonts causing FOUT.

### 46. What is the difference between application errors and infrastructure errors?
**Answer**: Application errors: JavaScript exceptions, API failures. Infrastructure errors: server crashes, network issues, database failures.

### 47. How do you correlate business metrics with technical metrics?
**Answer**: Track custom events for business actions, correlate with performance and error data:
```javascript
newrelic.addPageAction('Purchase', { 
  amount: 100,
  productCount: 5 
});
```

### 48. What is canary deployment monitoring?
**Answer**: Monitoring specific deployments to catch issues before rolling out to all users, comparing metrics between versions.

### 49. How do you handle data retention in monitoring?
**Answer**: Balance cost and insight - aggregate old data, adjust sampling rates, and archive detailed data based on retention policies.

### 50. What is the difference between push and pull monitoring?
**Answer**: 
- **Push**: Agent sends data to monitoring backend
- **Pull**: Monitoring backend scrapes metrics from agent

### 51. How do you monitor web worker performance?
**Answer**: Track web worker creation, message passing, and execution time within the worker context.

### 52. What is the difference between synthetic and real user monitoring?
**Answer**: RUM captures actual users, synthetic simulates users. Both needed for complete picture.

### 53. How do you implement feature flag monitoring?
**Answer**: Track feature flag state and correlate with performance/error metrics:
```javascript
newrelic.addPageAction('FeatureFlagEnabled', { 
  feature: 'newCheckout',
  enabled: true 
});
```

### 54. What is long task monitoring?
**Answer**: Identifies JavaScript tasks taking longer than 50ms, blocking main thread and hurting responsiveness.

### 55. How do you monitor PWA (Progressive Web App) performance?
**Answer**: Monitor service worker registration, cache hit rates, offline functionality, and install metrics.

### 56. What is the difference between error rate and error budget?
**Answer**: Error rate is actual error percentage, error budget is allowed error rate based on SLO.

### 57. How do you debug performance issues with monitoring data?
**Answer**: Combine waterfall views, session replay, logs, and traces. Look for correlation between changes and performance impact.

### 58. What is correlation analysis?
**Answer**: Finding relationships between different metrics (e.g., does page load time correlate with bounce rate?).

### 59. How do you implement real-time dashboards?
**Answer**: Use streaming data, auto-refresh intervals, focus on key metrics, and configure critical alerts.

### 60. What is the purpose of status pages?
**Answer**: Public facing page showing service status and incidents. Reduces support load and maintains customer trust.

### 61. How do you monitor API gateways?
**Answer**: Track request rate, response time, error rate, latency, and payload sizes at gateway level.

### 62. What is the difference between client-side and server-side monitoring?
**Answer**: Client-side: user experience, browser performance. Server-side: backend performance, business logic execution.

### 63. How do you handle cross-domain tracking?
**Answer**: Use monitoring SDK functionality or custom headers to correlate requests across domain boundaries.

### 64. What is the purpose of anomaly detection?
**Answer**: Automatically identifies unusual patterns in metrics without predefined thresholds.

### 65. How do you calculate availability?
**Answer**: Availability = (Total Time - Downtime) / Total Time × 100%.

### 66. What is chaos engineering in monitoring context?
**Answer**: Intentionally introducing failures to test monitoring effectiveness and incident response procedures.

### 67. How do you monitor WebSocket connections?
**Answer**: Track connection establishment, message rates, latency, and disconnection events.

### 68. What is the difference between log aggregation and log streaming?
**Answer**: Aggregation collects logs after the fact, streaming processes logs in real-time.

### 69. How do you implement cost optimization in monitoring?
**Answer**: Adjust sampling rates, reduce retention period, aggregate data, remove unnecessary metrics, use reserved instances.

### 70. What is the purpose of runbooks?
**Answer**: Documentation of how to respond to specific alerts, reducing MTTR and ensuring consistency.

### 71. How do you monitor database performance from frontend?
**Answer**: Monitor API response times (proxy for database performance), track query execution times if exposed via API.

### 72. What is the difference between incident and alert?
**Answer**: Alert is a notification triggered by threshold violation. Incident is when alert indicates an actual issue requiring response.

### 73. How do you implement blameless post-mortems?
**Answer**: Focus on system and process failures rather than individual actions, extract learnings, implement preventive measures.

### 74. What is the difference between baseline and threshold?
**Answer**: Baseline is normal behavior pattern, threshold is alert trigger point based on deviations from baseline.

### 75. How do you monitor edge computing performance?
**Answer**: Monitor edge function execution time, latency from edge to origin, cache hit rates, and geographic performance variations.

### 76. What is the purpose of tracing in monitoring?
**Answer**: Shows request flow through system, identifying bottlenecks and latency sources across distributed services.

### 77. How do you handle data volume reduction?
**Answer**: Sampling, filtering, aggregation, compression, and selective tracking of high-value data.

### 78. What is the difference between metrics and time-series data?
**Answer**: Metrics are measurements, time-series is metrics ordered by time enabling trend analysis.

### 79. How do you implement on-call rotations?
**Answer**: Define escalation policies, primary/secondary on-call, maintain runbooks, conduct regular training.

### 80. What is the purpose of service level indicators (SLIs)?
**Answer**: Specific metrics measuring how well service meets SLOs. Example: error rate, latency, availability.

### 81. How do you monitor modal and popup performance?
**Answer**: Track modal render time, animation smoothness, interaction responsiveness, and dismiss patterns.

### 82. What is the difference between active monitoring and passive monitoring?
**Answer**: Active: synthetic tests simulating users. Passive: RUM tracking actual users.

### 83. How do you implement cost allocation in monitoring?
**Answer**: Tag resources by team/project, track usage per tag, allocate costs based on consumption.

### 84. What is the purpose of instrumentation?
**Answer**: Adding monitoring code to capture performance data and events throughout application.

### 85. How do you monitor local storage and session storage access?
**Answer**: Intercept storage operations, track success/failure, monitor quota usage.

### 86. What is the difference between whitebox and blackbox monitoring?
**Answer**: Whitebox: internal metrics from within system. Blackbox: external perspective of system behavior.

### 87. How do you implement budget-aware deployments?
**Answer**: Use monitoring data to make deployment decisions, rollback based on error rate thresholds, implement canary releases.

### 88. What is the purpose of capacity planning?
**Answer**: Using historical monitoring data to predict future needs and provision resources appropriately.

### 89. How do you monitor authentication and security events?
**Answer**: Track login attempts, failed authentications, suspicious activity, and correlate with performance metrics.

### 90. What is the difference between deterministic and probabilistic sampling?
**Answer**: Deterministic: consistent sampling based on user ID. Probabilistic: random sampling at fixed rate.

### 91. How do you implement health checks?
**Answer**: Periodic calls to endpoint verifying service health, monitored by external systems.

### 92. What is the purpose of metric cardinality?
**Answer**: Number of unique values for a metric dimension. High cardinality increases storage and processing costs.

### 93. How do you monitor infinite scroll performance?
**Answer**: Track DOM node count, scroll event frequency, memory usage, and virtual scrolling implementation effectiveness.

### 94. What is the difference between trace and span?
**Answer**: Trace is complete request journey, spans are individual operations within trace.

### 95. How do you implement white-glove monitoring?
**Answer**: Dedicated monitoring for premium customers, higher sampling rates, custom dashboards, priority alerting.

### 96. What is the purpose of SLO calculation methods?
**Answer**: Different calculation approaches (simple average, percentile-based, time-based) for different use cases.

### 97. How do you monitor carousel and slider performance?
**Answer**: Track carousel render time, slide transitions, lazy loading effectiveness, interaction responsiveness.

### 98. What is the difference between granular and coarse-grained metrics?
**Answer**: Granular: detailed per-operation metrics. Coarse-grained: aggregate metrics across many operations.

### 99. How do you implement progressive enhancement in monitoring?
**Answer**: Track functionality availability across different browser capabilities, monitor feature usage by browser type.

### 100. What are best practices for front-end monitoring?
**Answer**:
- Start with Core Web Vitals and custom business metrics
- Implement error tracking and session replay for debugging
- Use appropriate sampling to manage costs
- Set meaningful alerts based on business impact
- Regularly review and optimize monitored metrics
- Correlate technical and business metrics
- Document runbooks for common issues
- Conduct regular training for incident response
- Use both RUM and synthetic monitoring
- Maintain data privacy and compliance
- Version monitoring configuration with code
- Test monitoring in staging before production

---

## Best Practices

### Effective Monitoring Strategy
1. **Define Clear Objectives**: Know what you want to monitor and why
2. **Focus on User Experience**: Prioritize user-visible metrics
3. **Balance Coverage and Cost**: Don't monitor everything, sample intelligently
4. **Automate Alerting**: Alert on meaningful anomalies, not noise
5. **Make Data Actionable**: Dashboards should enable quick decisions

### Data Collection Best Practices
```javascript
// Comprehensive instrumentation
{
  // Performance metrics
  performance: {
    trackWebVitals: true,
    trackResourceTiming: true,
    trackLongTasks: true
  },
  // Error tracking
  errors: {
    captureExceptions: true,
    captureUnhandledRejections: true,
    logConsoleErrors: true
  },
  // User interactions
  interactions: {
    trackClicks: true,
    trackNavigation: true,
    trackForms: true
  },
  // Privacy
  privacy: {
    redactPII: true,
    maskInputs: true,
    blockMedia: true
  }
}
```

### Alert Configuration Best Practices
- Alert on deviations from baseline, not absolute values
- Set alert thresholds based on business impact
- Avoid alert fatigue by tuning sensitivity
- Implement escalation policies
- Use multiple notification channels
- Require acknowledgment for critical alerts

### Dashboard Design Best Practices
- Focus on key metrics (3-5 max)
- Use clear visualization types
- Implement drill-down capabilities
- Show trends and historical comparison
- Update dashboards regularly based on feedback

---

## Tools Comparison Table

| Feature | New Relic | Sentry | Datadog | LogRocket |
|---------|-----------|--------|---------|-----------|
| Error Tracking | ✅ | ✅✅ | ✅ | ✅ |
| Session Replay | ✅ | ✅ | ✅ | ✅✅ |
| Performance Monitoring | ✅✅ | ✅ | ✅✅ | ✅ |
| Infrastructure Monitoring | ✅ | ❌ | ✅✅ | ❌ |
| RUM | ✅✅ | ✅ | ✅✅ | ✅ |
| Synthetic Monitoring | ✅ | ❌ | ✅ | ❌ |
| Distributed Tracing | ✅✅ | ✅ | ✅✅ | ❌ |
| Alerting | ✅ | ✅ | ✅ | Limited |
| Custom Dashboards | ✅✅ | ✅ | ✅✅ | ✅ |
| Pricing Model | Pay-per-GB | Monthly | Pay-per-metric | Seats |

---

## Troubleshooting Guide

### High Error Rates
1. Check if errors are critical or informational
2. Verify error is not noise (network issues, user actions)
3. Check error logs for stack traces
4. Use session replay to see user context
5. Check recent code deployments

### Performance Degradation
1. Use Lighthouse CI for regression detection
2. Check resource loading times
3. Verify no unexpected script injections
4. Check third-party script impact
5. Analyze user's network/device context

### Missing Data
1. Verify monitoring agent is initialized
2. Check network requests to monitoring backend
3. Verify API key/token configuration
4. Check browser console for errors
5. Verify sampling rate isn't too low

### False Alerts
1. Adjust threshold sensitivity
2. Increase alert duration
3. Implement baseline-based alerting
4. Exclude known maintenance windows
5. Use smart alerting (anomaly detection)

---

## Future Trends

### Observability Evolution
- **AI-Driven Insights**: Automatic anomaly detection and root cause analysis
- **OpenTelemetry**: Standardized instrumentation across tools
- **Real-Time Dashboards**: Streaming data and faster insights
- **Serverless Optimization**: Better monitoring for FaaS architectures
- **Edge Computing**: Monitoring across distributed edge networks
- **Composable Observability**: Mix-and-match best-of-breed tools

---

## Resources

- [New Relic Documentation](https://docs.newrelic.com/)
- [Sentry Documentation](https://docs.sentry.io/)
- [Datadog Documentation](https://docs.datadoghq.com/)
- [Web Vitals Guide](https://web.dev/vitals/)
- [Google Analytics 4 Docs](https://support.google.com/analytics)
- [Lighthouse Documentation](https://developers.google.com/web/tools/lighthouse)
- [MDN Performance API](https://developer.mozilla.org/en-US/docs/Web/API/Performance)
- [LogRocket Documentation](https://docs.logrocket.com/)
- [OpenTelemetry](https://opentelemetry.io/)
- [Prometheus Monitoring](https://prometheus.io/)
