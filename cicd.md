# CI/CD - Senior Developer to Architect Level Q&A

## Table of Contents
1. [Core Concepts](#core-concepts)
2. [Pipeline Design](#pipeline-design)
3. [Testing Strategies](#testing-strategies)
4. [Deployment Strategies](#deployment-strategies)
5. [Security & Best Practices](#security--best-practices)
6. [Tools & Implementation](#tools--implementation)

---

## Core Concepts

### Q1: Explain the difference between Continuous Integration, Continuous Delivery, and Continuous Deployment.

**Answer:**

**Simple Analogy:**
Think of building a car on an assembly line:

**Continuous Integration (CI):**
- Each worker adds their part and the car is tested immediately
- If something breaks, you know exactly who added what

**Continuous Delivery (CD):**
- Car is fully built and ready to ship anytime
- Manager decides when to actually ship

**Continuous Deployment:**
- Car automatically ships to customer as soon as it's ready
- No manual approval needed

---

**Detailed Breakdown:**

**1. Continuous Integration (CI):**
Developers merge code frequently, automated builds and tests run on every commit.

```yaml
# .github/workflows/ci.yml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install dependencies
        run: npm install
      - name: Run linter
        run: npm run lint
      - name: Run tests
        run: npm test
      - name: Build
        run: npm run build
```

**Benefits:**
- Catch bugs early
- Reduce integration problems
- Improve code quality
- Faster development

**2. Continuous Delivery:**
Code is always in a deployable state, but deployment is manual.

```yaml
# Build → Test → Stage → [Manual Approval] → Production

jobs:
  deploy-staging:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to staging
        run: ./deploy-staging.sh
  
  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production  # Requires manual approval
    steps:
      - name: Deploy to production
        run: ./deploy-production.sh
```

**Benefits:**
- Reduce deployment risk
- Deploy anytime with confidence
- Faster time to market
- Business decides when to release

**3. Continuous Deployment:**
Every passing commit automatically goes to production.

```yaml
# Build → Test → Stage → Production (automatic)

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Run tests
        run: npm test
      - name: Deploy to production
        if: success()
        run: ./deploy-production.sh
```

**Benefits:**
- Fastest time to market
- Immediate user feedback
- No manual bottleneck

**Requires:**
- Excellent test coverage
- Monitoring and rollback capability
- Feature flags
- Mature team

---

**Comparison:**

| Aspect | CI | Continuous Delivery | Continuous Deployment |
|--------|----|--------------------|---------------------|
| Frequency | Every commit | Ready anytime | Every commit |
| Testing | Automated | Automated | Automated |
| Deployment | Manual | Manual | Automatic |
| Production | No | Yes (manual) | Yes (auto) |
| Risk | Low | Low-Medium | Medium-High |

---

### Q2: What are the key components of a CI/CD pipeline?

**Answer:**

**Pipeline Stages:**

```
Code → Build → Test → Package → Deploy → Monitor
```

**1. Source/Trigger:**
```yaml
# Trigger on events
on:
  push:
    branches: [main, develop]
  pull_request:
  schedule:
    - cron: '0 0 * * *'  # Daily
  workflow_dispatch:  # Manual trigger
```

**2. Build:**
```yaml
build:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v3
    
    # Install dependencies
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    # Compile/Build
    - name: Build application
      run: npm run build
    
    # Cache build artifacts
    - name: Upload build artifacts
      uses: actions/upload-artifact@v3
      with:
        name: build
        path: dist/
```

**3. Test:**
```yaml
test:
  runs-on: ubuntu-latest
  needs: build
  strategy:
    matrix:
      node-version: [16, 18, 20]
  
  steps:
    # Unit tests
    - name: Unit tests
      run: npm run test:unit
    
    # Integration tests
    - name: Integration tests
      run: npm run test:integration
    
    # E2E tests
    - name: E2E tests
      run: npm run test:e2e
    
    # Code coverage
    - name: Upload coverage
      uses: codecov/codecov-action@v3
    
    # Security scanning
    - name: Security scan
      run: npm audit
```

**4. Quality Gates:**
```yaml
quality:
  runs-on: ubuntu-latest
  steps:
    # Code quality
    - name: SonarQube scan
      run: sonar-scanner
    
    # Coverage threshold
    - name: Check coverage
      run: |
        coverage=$(cat coverage/coverage-summary.json | jq .total.lines.pct)
        if [ $(echo "$coverage < 80" | bc) -eq 1 ]; then
          echo "Coverage below 80%"
          exit 1
        fi
    
    # Linting
    - name: ESLint
      run: npm run lint
```

**5. Package:**
```yaml
package:
  runs-on: ubuntu-latest
  needs: [test, quality]
  steps:
    # Docker build
    - name: Build Docker image
      run: docker build -t myapp:${{ github.sha }} .
    
    # Push to registry
    - name: Push to Docker Hub
      run: |
        docker login -u ${{ secrets.DOCKER_USERNAME }} -p ${{ secrets.DOCKER_PASSWORD }}
        docker push myapp:${{ github.sha }}
```

**6. Deploy:**
```yaml
deploy:
  runs-on: ubuntu-latest
  needs: package
  steps:
    # Deploy to staging
    - name: Deploy to staging
      run: |
        kubectl set image deployment/myapp myapp=myapp:${{ github.sha }}
        kubectl rollout status deployment/myapp
    
    # Smoke tests
    - name: Smoke tests
      run: npm run test:smoke
    
    # Deploy to production (if staging passes)
    - name: Deploy to production
      if: github.ref == 'refs/heads/main'
      run: ./deploy-production.sh
```

**7. Monitor:**
```yaml
post-deploy:
  runs-on: ubuntu-latest
  needs: deploy
  steps:
    # Health check
    - name: Health check
      run: |
        response=$(curl -s -o /dev/null -w "%{http_code}" https://myapp.com/health)
        if [ $response -ne 200 ]; then
          echo "Health check failed"
          # Trigger rollback
          exit 1
        fi
    
    # Notify team
    - name: Slack notification
      uses: 8398a7/action-slack@v3
      with:
        status: ${{ job.status }}
        text: 'Deployment completed'
```

---

## Pipeline Design

### Q3: How do you design a multi-environment CI/CD pipeline (dev, staging, production)?

**Answer:**

**Architecture:**

```
Feature Branch → Dev (auto)
     ↓
Pull Request → Staging (auto)
     ↓
Main Branch → Production (manual approval)
```

**Implementation:**

```yaml
# .github/workflows/pipeline.yml
name: Multi-Environment Pipeline

on:
  push:
    branches:
      - main
      - develop
      - 'feature/**'
  pull_request:
    branches:
      - main
      - develop

jobs:
  # ===== BUILD & TEST =====
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Build
        run: npm run build
      
      - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-${{ github.sha }}
          path: dist/

  # ===== DEPLOY TO DEV =====
  deploy-dev:
    needs: build-test
    if: github.ref == 'refs/heads/develop' || startsWith(github.ref, 'refs/heads/feature/')
    runs-on: ubuntu-latest
    environment:
      name: development
      url: https://dev.myapp.com
    
    steps:
      - name: Download artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-${{ github.sha }}
      
      - name: Deploy to Dev
        run: |
          aws s3 sync dist/ s3://dev-myapp-bucket
          aws cloudfront create-invalidation --distribution-id $DEV_DISTRIBUTION
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.DEV_AWS_ACCESS_KEY }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.DEV_AWS_SECRET_KEY }}
      
      - name: Run smoke tests
        run: npm run test:smoke -- --url=https://dev.myapp.com

  # ===== DEPLOY TO STAGING =====
  deploy-staging:
    needs: build-test
    if: github.event_name == 'pull_request' && github.base_ref == 'main'
    runs-on: ubuntu-latest
    environment:
      name: staging
      url: https://staging.myapp.com
    
    steps:
      - name: Download artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-${{ github.sha }}
      
      - name: Deploy to Staging
        run: |
          kubectl config use-context staging
          kubectl set image deployment/myapp myapp=myapp:${{ github.sha }}
          kubectl rollout status deployment/myapp
        env:
          KUBECONFIG: ${{ secrets.STAGING_KUBECONFIG }}
      
      - name: Integration tests
        run: npm run test:integration -- --url=https://staging.myapp.com
      
      - name: Performance tests
        run: npm run test:performance -- --url=https://staging.myapp.com

  # ===== DEPLOY TO PRODUCTION =====
  deploy-production:
    needs: build-test
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://myapp.com
    
    steps:
      - name: Download artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-${{ github.sha }}
      
      # Blue-Green Deployment
      - name: Deploy to Production (Blue)
        run: |
          kubectl config use-context production
          
          # Deploy to blue environment
          kubectl set image deployment/myapp-blue myapp=myapp:${{ github.sha }}
          kubectl rollout status deployment/myapp-blue
          
          # Smoke tests on blue
          npm run test:smoke -- --url=https://blue.myapp.com
        env:
          KUBECONFIG: ${{ secrets.PROD_KUBECONFIG }}
      
      - name: Switch traffic to Blue
        run: |
          # Switch load balancer to blue
          kubectl patch service myapp -p '{"spec":{"selector":{"version":"blue"}}}'
      
      - name: Monitor for 5 minutes
        run: |
          sleep 300
          # Check error rates
          if [ $(check_error_rate) -gt 1 ]; then
            echo "High error rate detected, rolling back"
            kubectl patch service myapp -p '{"spec":{"selector":{"version":"green"}}}'
            exit 1
          fi
      
      - name: Notify deployment
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Production deployment ${{ job.status }}'
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

**Environment-Specific Configuration:**

```javascript
// config/environments.js
const environments = {
  development: {
    apiUrl: 'https://dev-api.myapp.com',
    dbHost: 'dev-db.myapp.com',
    logLevel: 'debug',
    features: {
      newFeature: true // Enable all features in dev
    }
  },
  staging: {
    apiUrl: 'https://staging-api.myapp.com',
    dbHost: 'staging-db.myapp.com',
    logLevel: 'info',
    features: {
      newFeature: true
    }
  },
  production: {
    apiUrl: 'https://api.myapp.com',
    dbHost: 'prod-db.myapp.com',
    logLevel: 'error',
    features: {
      newFeature: false // Controlled rollout
    }
  }
};

module.exports = environments[process.env.NODE_ENV || 'development'];
```

---

## Testing Strategies

### Q4: What types of tests should be in a CI/CD pipeline and in what order?

**Answer:**

**Testing Pyramid:**

```
        /\
       /  \  E2E (UI Tests)
      /____\
     /      \  Integration Tests
    /________\
   /          \  Unit Tests
  /__________  \
```

**Test Order (Fast to Slow):**

**1. Static Analysis (Seconds):**
```yaml
static-analysis:
  steps:
    # Linting
    - name: ESLint
      run: npm run lint
    
    # Type checking
    - name: TypeScript
      run: npx tsc --noEmit
    
    # Security scan
    - name: npm audit
      run: npm audit --audit-level=high
```

**2. Unit Tests (Seconds to Minutes):**
```yaml
unit-tests:
  steps:
    - name: Run unit tests
      run: npm run test:unit -- --coverage
    
    - name: Coverage check
      run: |
        if [ $(coverage-percentage) -lt 80 ]; then
          echo "Coverage below threshold"
          exit 1
        fi
```

```javascript
// example.test.js
describe('calculateTotal', () => {
  it('should sum array of numbers', () => {
    expect(calculateTotal([1, 2, 3])).toBe(6);
  });
  
  it('should handle empty array', () => {
    expect(calculateTotal([])).toBe(0);
  });
  
  it('should ignore non-numbers', () => {
    expect(calculateTotal([1, 'two', 3])).toBe(4);
  });
});
```

**3. Integration Tests (Minutes):**
```yaml
integration-tests:
  services:
    postgres:
      image: postgres:15
      env:
        POSTGRES_PASSWORD: test
    redis:
      image: redis:7
  
  steps:
    - name: Run integration tests
      run: npm run test:integration
      env:
        DATABASE_URL: postgresql://postgres:test@postgres:5432/test
        REDIS_URL: redis://redis:6379
```

```javascript
// api.integration.test.js
describe('User API', () => {
  beforeAll(async () => {
    await database.connect();
  });
  
  it('should create and retrieve user', async () => {
    // Create user
    const response = await request(app)
      .post('/api/users')
      .send({ name: 'John', email: 'john@example.com' });
    
    expect(response.status).toBe(201);
    
    // Retrieve user
    const getResponse = await request(app)
      .get(`/api/users/${response.body.id}`);
    
    expect(getResponse.body.name).toBe('John');
  });
});
```

**4. Contract Tests (Minutes):**
```yaml
contract-tests:
  steps:
    - name: Pact verification
      run: npm run test:pact
```

**5. E2E Tests (Minutes to Hours):**
```yaml
e2e-tests:
  steps:
    - name: Start application
      run: npm start &
      
    - name: Wait for app
      run: npx wait-on http://localhost:3000
    
    - name: Run Playwright tests
      run: npx playwright test
    
    - name: Upload test results
      if: always()
      uses: actions/upload-artifact@v3
      with:
        name: playwright-report
        path: playwright-report/
```

```javascript
// e2e/login.spec.js
test('user can login', async ({ page }) => {
  await page.goto('https://myapp.com');
  
  await page.fill('[data-testid="email"]', 'user@example.com');
  await page.fill('[data-testid="password"]', 'password123');
  await page.click('[data-testid="login-button"]');
  
  await expect(page).toHaveURL(/dashboard/);
  await expect(page.locator('[data-testid="user-name"]')).toContainText('John Doe');
});
```

**6. Performance Tests (Minutes):**
```yaml
performance-tests:
  steps:
    - name: Lighthouse CI
      run: lhci autorun
    
    - name: Load testing
      run: k6 run load-test.js
```

```javascript
// load-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 100 }, // Ramp up to 100 users
    { duration: '5m', target: 100 }, // Stay at 100 users
    { duration: '2m', target: 0 },   // Ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'], // 95% under 500ms
    http_req_failed: ['rate<0.01'],   // <1% errors
  },
};

export default function () {
  const res = http.get('https://myapp.com/api/users');
  check(res, {
    'status is 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timings.duration < 500,
  });
  sleep(1);
}
```

**7. Security Tests (Minutes):**
```yaml
security-tests:
  steps:
    # OWASP ZAP scan
    - name: ZAP scan
      run: docker run -v $(pwd):/zap/wrk/:rw -t owasp/zap2docker-stable zap-baseline.py -t https://myapp.com
    
    # Dependency scan
    - name: Snyk scan
      run: npx snyk test
```

**Parallelization:**
```yaml
test:
  strategy:
    matrix:
      test-suite: [unit, integration, e2e]
  
  steps:
    - name: Run ${{ matrix.test-suite }} tests
      run: npm run test:${{ matrix.test-suite }}
```

**Test Distribution:**
- **PR/Commit**: Unit + Integration
- **Merge to main**: All tests
- **Nightly**: Performance + Security
- **Pre-release**: Full suite including manual testing

---

## Deployment Strategies

### Q5: Compare different deployment strategies (Blue-Green, Canary, Rolling, Recreate).

**Answer:**

**1. Recreate (Simple but risky):**

```
V1: ████████ (Stop all)
     ↓
V2: ████████ (Start all)

Downtime: Yes
```

```yaml
# Kubernetes
spec:
  strategy:
    type: Recreate
```

**When to use:**
- Non-production environments
- Planned maintenance windows
- Stateful applications requiring complete restart

---

**2. Rolling Update (Gradual, no downtime):**

```
V1: ████████
V1: ███████▓ 
V1: ██████▓▓
V1: ███▓▓▓▓▓
V2: ▓▓▓▓▓▓▓▓

No downtime
```

```yaml
# Kubernetes rolling update
spec:
  replicas: 4
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # 1 extra pod during update
      maxUnavailable: 1  # Max 1 pod down
```

```bash
# Kubectl
kubectl set image deployment/myapp myapp=myapp:v2
kubectl rollout status deployment/myapp

# Rollback if needed
kubectl rollout undo deployment/myapp
```

**Pros:**
- No downtime
- Gradual rollout
- Easy rollback

**Cons:**
- Two versions running simultaneously
- Slower deployment

---

**3. Blue-Green (Instant switch):**

```
Blue (V1):  ████████ (100% traffic)
Green (V2): ████████ (0% traffic, testing)
     ↓ Switch
Blue (V1):  ████████ (0% traffic)
Green (V2): ████████ (100% traffic)
```

```yaml
# Kubernetes Blue-Green
---
# Blue deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: blue
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
      - name: myapp
        image: myapp:v1

---
# Green deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: green
  template:
    metadata:
      labels:
        app: myapp
        version: green
    spec:
      containers:
      - name: myapp
        image: myapp:v2

---
# Service (switches between blue and green)
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
    version: blue  # Change to 'green' to switch
  ports:
    - port: 80
      targetPort: 8080
```

```bash
# Deploy green
kubectl apply -f myapp-green.yaml

# Test green
curl https://green.myapp.com

# Switch traffic to green
kubectl patch service myapp -p '{"spec":{"selector":{"version":"green"}}}'

# Monitor, if issues rollback
kubectl patch service myapp -p '{"spec":{"selector":{"version":"blue"}}}'
```

**Pros:**
- Instant rollout/rollback
- Test in production before switch
- Zero downtime

**Cons:**
- Requires double resources
- Database migrations complex

---

**4. Canary (Gradual traffic shift):**

```
V1: ████████ (90% traffic)
V2: █        (10% traffic)
     ↓
V1: ████     (40% traffic)
V2: ████     (60% traffic)
     ↓
V1:          (0% traffic)
V2: ████████ (100% traffic)
```

```yaml
# Using Istio
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
  - myapp.com
  http:
  - match:
    - headers:
        user-type:
          exact: beta
    route:
    - destination:
        host: myapp
        subset: v2
  - route:
    - destination:
        host: myapp
        subset: v1
      weight: 90
    - destination:
        host: myapp
        subset: v2
      weight: 10
```

```yaml
# Gradually increase traffic
# Stage 1: 10%
# Monitor: error rate, latency, user feedback
# Stage 2: 25%
# Monitor...
# Stage 3: 50%
# Monitor...
# Stage 4: 100%
```

**Automated Canary with Flagger:**
```yaml
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: myapp
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  service:
    port: 80
  analysis:
    interval: 1m
    threshold: 5
    maxWeight: 50
    stepWeight: 10
    metrics:
    - name: request-success-rate
      thresholdRange:
        min: 99
    - name: request-duration
      thresholdRange:
        max: 500
```

**Pros:**
- Minimal risk
- Real user testing
- Gradual rollout
- Automated rollback on metrics

**Cons:**
- Complex setup
- Longer deployment time
- Requires monitoring

---

**Comparison:**

| Strategy | Downtime | Risk | Complexity | Rollback Speed | Cost |
|----------|----------|------|------------|---------------|------|
| Recreate | High | High | Low | Slow | Low |
| Rolling | None | Medium | Medium | Medium | Low |
| Blue-Green | None | Low | Medium | Instant | High (2x) |
| Canary | None | Very Low | High | Instant | Medium |

**Recommendations:**
- **Startups/Small teams**: Rolling Update
- **Enterprise/Critical apps**: Blue-Green
- **High-traffic apps**: Canary
- **Dev/Test**: Recreate

---

## Security & Best Practices

### Q6: How do you secure CI/CD pipelines and manage secrets?

**Answer:**

**1. Secret Management:**

**❌ Never do this:**
```yaml
# Bad: Hardcoded secrets
env:
  API_KEY: abc123secret
  DATABASE_URL: postgresql://user:password@host/db
```

**✅ Good: Use secret management:**
```yaml
# GitHub Actions
env:
  API_KEY: ${{ secrets.API_KEY }}
  DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

**Best Practices:**

**A. Use Vault/Secret Managers:**
```yaml
# HashiCorp Vault
- name: Import Secrets
  uses: hashicorp/vault-action@v2
  with:
    url: https://vault.mycompany.com
    token: ${{ secrets.VAULT_TOKEN }}
    secrets: |
      secret/data/prod/api | API_KEY ;
      secret/data/prod/db | DATABASE_URL

# AWS Secrets Manager
- name: Get secrets
  run: |
    API_KEY=$(aws secretsmanager get-secret-value --secret-id prod/api-key --query SecretString --output text)
    echo "::add-mask::$API_KEY"
    echo "API_KEY=$API_KEY" >> $GITHUB_ENV
```

**B. Rotate Secrets:**
```bash
# Script to rotate secrets
#!/bin/bash

# Generate new API key
NEW_KEY=$(openssl rand -hex 32)

# Update in Vault
vault kv put secret/api-key value=$NEW_KEY

# Update in application
kubectl create secret generic api-key --from-literal=key=$NEW_KEY --dry-run=client -o yaml | kubectl apply -f -

# Rotate old key after grace period
sleep 300
# Revoke old key
```

**C. Least Privilege:**
```yaml
# AWS IAM Policy for CI/CD
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::deploy-bucket/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ecr:GetAuthorizationToken",
        "ecr:BatchCheckLayerAvailability",
        "ecr:PutImage"
      ],
      "Resource": "*"
    }
  ]
}
```

**2. Pipeline Security:**

```yaml
security-checks:
  steps:
    # 1. Dependency scanning
    - name: Audit dependencies
      run: npm audit --audit-level=high
    
    # 2. Static code analysis
    - name: SonarQube
      run: sonar-scanner
    
    # 3. Secret scanning
    - name: GitLeaks
      run: |
        docker run --rm -v $(pwd):/path zricethezav/gitleaks:latest detect \
          --source /path --verbose --redact
    
    # 4. Container scanning
    - name: Trivy scan
      run: |
        docker build -t myapp:${{ github.sha }} .
        trivy image --severity HIGH,CRITICAL myapp:${{ github.sha }}
    
    # 5. SAST (Static Application Security Testing)
    - name: Semgrep
      run: semgrep --config=auto .
    
    # 6. License compliance
    - name: License check
      run: npx license-checker --production --onlyAllow 'MIT;Apache-2.0;BSD-3-Clause'
```

**3. Access Control:**
```yaml
# GitHub Actions - Environment protection
environments:
  production:
    # Required reviewers
    reviewers:
      - devops-team
      - security-team
    
    # Wait timer
    wait-timer: 30
    
    # Branch protection
    deployment_branch_policy:
      protected_branches: true
```

**4. Audit Logging:**
```yaml
- name: Log deployment
  run: |
    curl -X POST https://audit-log-service.com/api/log \
      -H "Content-Type: application/json" \
      -d '{
        "action": "deployment",
        "environment": "production",
        "user": "${{ github.actor }}",
        "commit": "${{ github.sha }}",
        "timestamp": "'$(date -u +%Y-%m-%dT%H:%M:%SZ)'"
      }'
```

**5. Immutable Infrastructure:**
```dockerfile
# Multi-stage build
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM node:18-alpine
# Create non-root user
RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001
WORKDIR /app
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
USER nodejs
EXPOSE 3000
CMD ["node", "dist/server.js"]
```

**6. Supply Chain Security:**
```yaml
# Sign images
- name: Sign container image
  run: |
    cosign sign --key cosign.key myapp:${{ github.sha }}

# Verify before deployment
- name: Verify signature
  run: |
    cosign verify --key cosign.pub myapp:${{ github.sha }}

# SBOM (Software Bill of Materials)
- name: Generate SBOM
  run: |
    syft myapp:${{ github.sha }} -o cyclonedx-json > sbom.json
    
- name: Upload SBOM
  uses: actions/upload-artifact@v3
  with:
    name: sbom
    path: sbom.json
```

---

## Tools & Implementation

### Q7: Compare popular CI/CD tools (GitHub Actions, GitLab CI, Jenkins, CircleCI). When to use each?

**Answer:**

**1. GitHub Actions:**

**Pros:**
- Native GitHub integration
- Free for public repos
- Large marketplace
- Easy to use

**Cons:**
- Limited to GitHub
- Can be expensive for private repos
- Less control than self-hosted

**Best for:**
- GitHub-hosted projects
- Open source
- Startups
- Simple to medium complexity

**Example:**
```yaml
name: CI/CD
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm test
```

---

**2. GitLab CI:**

**Pros:**
- Built into GitLab
- Self-hosted option
- Integrated with GitLab features
- Good Docker support

**Cons:**
- GitLab only
- Can be resource-intensive

**Best for:**
- GitLab users
- Teams wanting all-in-one (Git + CI/CD + Registry)
- Self-hosted requirements

**Example:**
```yaml
# .gitlab-ci.yml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - dist/

test:
  stage: test
  script:
    - npm test

deploy:
  stage: deploy
  script:
    - ./deploy.sh
  only:
    - main
```

---

**3. Jenkins:**

**Pros:**
- Highly customizable
- Huge plugin ecosystem
- Self-hosted (full control)
- Free and open source

**Cons:**
- Complex setup
- Requires maintenance
- UI feels dated
- Steep learning curve

**Best for:**
- Large enterprises
- Complex pipelines
- Need full control
- Legacy systems

**Example:**
```groovy
// Jenkinsfile
pipeline {
  agent any
  
  stages {
    stage('Build') {
      steps {
        sh 'npm ci'
        sh 'npm run build'
      }
    }
    
    stage('Test') {
      steps {
        sh 'npm test'
      }
    }
    
    stage('Deploy') {
      when {
        branch 'main'
      }
      steps {
        sh './deploy.sh'
      }
    }
  }
  
  post {
    always {
      junit 'test-results/*.xml'
    }
    failure {
      mail to: 'team@example.com',
           subject: "Failed: ${currentBuild.fullDisplayName}",
           body: "Build failed"
    }
  }
}
```

---

**4. CircleCI:**

**Pros:**
- Fast execution
- Good Docker support
- Clean UI
- Parallelization

**Cons:**
- Can be expensive
- Less flexibility than Jenkins

**Best for:**
- Docker-heavy workflows
- Performance-critical
- Medium to large teams

**Example:**
```yaml
# .circleci/config.yml
version: 2.1

jobs:
  build:
    docker:
      - image: cimg/node:18.0
    steps:
      - checkout
      - restore_cache:
          keys:
            - npm-cache-{{ checksum "package-lock.json" }}
      - run: npm ci
      - save_cache:
          key: npm-cache-{{ checksum "package-lock.json" }}
          paths:
            - ~/.npm
      - run: npm test
      - run: npm run build
      - persist_to_workspace:
          root: .
          paths:
            - dist

  deploy:
    docker:
      - image: cimg/node:18.0
    steps:
      - attach_workspace:
          at: .
      - run: ./deploy.sh

workflows:
  version: 2
  build-deploy:
    jobs:
      - build
      - deploy:
          requires:
            - build
          filters:
            branches:
              only: main
```

---

**Comparison Table:**

| Feature | GitHub Actions | GitLab CI | Jenkins | CircleCI |
|---------|---------------|-----------|---------|----------|
| Hosting | Cloud/Self | Cloud/Self | Self | Cloud/Self |
| Setup | Easy | Easy | Complex | Easy |
| Cost | Free tier | Free tier | Free | Paid |
| Integration | GitHub | GitLab | Any | Any |
| Learning Curve | Low | Low | High | Low |
| Customization | Medium | Medium | Very High | Medium |
| Best For | GitHub projects | GitLab projects | Enterprises | Docker workflows |

---

## Summary

**CI/CD Best Practices:**

1. **Automate Everything**: From build to production
2. **Fast Feedback**: Run fast tests first
3. **Security First**: Scan, audit, rotate secrets
4. **Fail Fast**: Catch issues early
5. **Immutable Artifacts**: Build once, deploy many times
6. **Progressive Deployment**: Canary, blue-green
7. **Monitor & Alert**: Know when something breaks
8. **Rollback Ready**: Always have an escape hatch
9. **Documentation**: Document pipeline and runbooks
10. **Team Culture**: Everyone is responsible for CI/CD

**Remember:** A good CI/CD pipeline is invisible when working, obvious when broken!
