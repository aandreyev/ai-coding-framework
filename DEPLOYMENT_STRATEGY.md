# Deployment Strategy

This document provides deployment guidance for AI assistants to build reliable, automated deployment pipelines. Follow these patterns to create consistent, secure, and efficient deployments.

## Core Deployment Principles

### 1. Automated Everything
**Rule:** Automate all deployment processes to eliminate human error and ensure consistency.

```yaml
# Example CI/CD Pipeline
name: Deploy Application

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Tests
        run: |
          npm install
          npm test
          npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to Production
        run: |
          docker build -t myapp:latest .
          docker push registry.example.com/myapp:latest
          kubectl apply -f k8s/
```

### 2. Environment Parity
**Rule:** Ensure development, staging, and production environments are as similar as possible.

```dockerfile
# Dockerfile - Same image across all environments
FROM node:18-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./
RUN npm ci --only=production

# Copy application code
COPY . .

# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001
USER nodejs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

CMD ["npm", "start"]
```

### 3. Blue-Green Deployments
**Rule:** Use blue-green deployments for zero-downtime updates.

```yaml
# Kubernetes Blue-Green Deployment
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
        image: myapp:v1.0.0
        ports:
        - containerPort: 3000
        readinessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
    version: blue  # Switch between blue/green
  ports:
  - port: 80
    targetPort: 3000
```

## CI/CD Pipeline Implementation

### 1. Multi-Stage Pipeline
**Pattern:** Implement progressive validation through multiple stages.

```yaml
# Complete CI/CD Pipeline
stages:
  - name: "Code Quality"
    jobs:
      - lint
      - security-scan
      - dependency-check
  
  - name: "Testing"
    jobs:
      - unit-tests
      - integration-tests
      - api-tests
  
  - name: "Build & Package"
    jobs:
      - build-application
      - build-docker-image
      - vulnerability-scan
  
  - name: "Deploy Staging"
    jobs:
      - deploy-to-staging
      - smoke-tests
      - e2e-tests
  
  - name: "Deploy Production"
    jobs:
      - deploy-to-production
      - health-checks
      - monitoring-alerts

# Quality Gates
quality_gates:
  code_coverage: 80
  security_score: 7
  performance_threshold: 2000ms
  error_rate: 0.1%
```

### 2. Environment-Specific Configuration
**Pattern:** Use environment variables and configuration management.

```bash
# Environment Configuration
# .env.staging
NODE_ENV=staging
DATABASE_URL=${STAGING_DATABASE_URL}
REDIS_URL=${STAGING_REDIS_URL}
API_BASE_URL=https://api-staging.example.com
LOG_LEVEL=debug

# .env.production
NODE_ENV=production
DATABASE_URL=${PRODUCTION_DATABASE_URL}
REDIS_URL=${PRODUCTION_REDIS_URL}
API_BASE_URL=https://api.example.com
LOG_LEVEL=info
```

```yaml
# Kubernetes ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  NODE_ENV: "production"
  LOG_LEVEL: "info"
  API_BASE_URL: "https://api.example.com"
---
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
data:
  DATABASE_URL: <base64-encoded-value>
  REDIS_URL: <base64-encoded-value>
```

## Database Migration Strategy

### 1. Automated Migrations
**Pattern:** Include database migrations in deployment pipeline.

```javascript
// Migration script example
const migrations = [
  {
    version: '001',
    description: 'Create users table',
    up: async (db) => {
      await db.query(`
        CREATE TABLE users (
          id SERIAL PRIMARY KEY,
          email VARCHAR(255) UNIQUE NOT NULL,
          created_at TIMESTAMP DEFAULT NOW()
        )
      `);
    },
    down: async (db) => {
      await db.query('DROP TABLE users');
    }
  }
];

// Migration runner
async function runMigrations() {
  const currentVersion = await getCurrentVersion();
  const pendingMigrations = migrations.filter(m => m.version > currentVersion);
  
  for (const migration of pendingMigrations) {
    try {
      console.log(`Running migration ${migration.version}: ${migration.description}`);
      await migration.up(db);
      await updateVersion(migration.version);
      console.log(`Migration ${migration.version} completed`);
    } catch (error) {
      console.error(`Migration ${migration.version} failed:`, error);
      throw error;
    }
  }
}
```

### 2. Rollback Strategy
**Pattern:** Always prepare rollback procedures.

```bash
#!/bin/bash
# rollback.sh - Automated rollback script

PREVIOUS_VERSION=${1:-$(get_previous_version)}

echo "Rolling back to version: $PREVIOUS_VERSION"

# Rollback application
kubectl set image deployment/myapp myapp=myapp:$PREVIOUS_VERSION

# Wait for rollout
kubectl rollout status deployment/myapp

# Rollback database if needed
if [ "$2" = "--with-db" ]; then
    echo "Rolling back database..."
    npm run migrate:rollback -- --to=$PREVIOUS_VERSION
fi

# Verify rollback
curl -f http://api.example.com/health || {
    echo "Rollback verification failed"
    exit 1
}

echo "Rollback completed successfully"
```

## Monitoring and Health Checks

### 1. Application Health Endpoints
**Pattern:** Implement comprehensive health checks.

```javascript
// Health check endpoint
app.get('/health', async (req, res) => {
  const checks = {
    timestamp: new Date().toISOString(),
    status: 'ok',
    version: process.env.APP_VERSION,
    checks: {}
  };

  try {
    // Database connectivity
    await db.query('SELECT 1');
    checks.checks.database = { status: 'ok' };
  } catch (error) {
    checks.checks.database = { status: 'error', error: error.message };
    checks.status = 'error';
  }

  try {
    // Redis connectivity
    await redis.ping();
    checks.checks.redis = { status: 'ok' };
  } catch (error) {
    checks.checks.redis = { status: 'error', error: error.message };
    checks.status = 'error';
  }

  // External API connectivity
  try {
    const response = await fetch('https://external-api.example.com/health', { timeout: 5000 });
    checks.checks.external_api = { 
      status: response.ok ? 'ok' : 'error',
      response_time: response.headers.get('x-response-time')
    };
  } catch (error) {
    checks.checks.external_api = { status: 'error', error: error.message };
  }

  const statusCode = checks.status === 'ok' ? 200 : 503;
  res.status(statusCode).json(checks);
});
```

### 2. Deployment Verification
**Pattern:** Automated post-deployment verification.

```bash
#!/bin/bash
# verify-deployment.sh

DEPLOY_ENV=${1:-staging}
BASE_URL="https://api-${DEPLOY_ENV}.example.com"

echo "Verifying deployment on $DEPLOY_ENV..."

# Health check
echo "Checking health endpoint..."
curl -f "$BASE_URL/health" || {
    echo "Health check failed"
    exit 1
}

# API functionality test
echo "Testing core API endpoints..."
curl -f "$BASE_URL/api/users" -H "Authorization: Bearer $TEST_TOKEN" || {
    echo "API test failed"
    exit 1
}

# Database connectivity
echo "Testing database operations..."
curl -f "$BASE_URL/api/test/db" -X POST || {
    echo "Database test failed"
    exit 1
}

# Performance check
echo "Checking response times..."
RESPONSE_TIME=$(curl -o /dev/null -s -w "%{time_total}" "$BASE_URL/api/users")
if (( $(echo "$RESPONSE_TIME > 2.0" | bc -l) )); then
    echo "Response time too slow: ${RESPONSE_TIME}s"
    exit 1
fi

echo "Deployment verification passed"
```

## Security in Deployment

### 1. Secrets Management
**Pattern:** Never hardcode secrets in deployment configurations.

```yaml
# GitHub Actions with secrets
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Kubernetes
        env:
          KUBE_CONFIG: ${{ secrets.KUBE_CONFIG }}
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          API_KEY: ${{ secrets.API_KEY }}
        run: |
          echo "$KUBE_CONFIG" | base64 -d > kubeconfig
          export KUBECONFIG=kubeconfig
          kubectl apply -f k8s/
```

### 2. Image Security
**Pattern:** Scan container images for vulnerabilities.

```yaml
# Security scanning in pipeline
- name: Run Trivy vulnerability scanner
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: 'myapp:latest'
    format: 'sarif'
    output: 'trivy-results.sarif'

- name: Upload Trivy scan results
  uses: github/codeql-action/upload-sarif@v2
  with:
    sarif_file: 'trivy-results.sarif'
```

## Error Handling and Recovery

### 1. Deployment Failure Recovery
**Pattern:** Automatic rollback on deployment failure.

```yaml
# Kubernetes deployment with automatic rollback
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  progressDeadlineSeconds: 300  # 5 minutes timeout
  template:
    spec:
      containers:
      - name: myapp
        image: myapp:latest
        readinessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 5
          failureThreshold: 3
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
          failureThreshold: 3
```

### 2. Monitoring and Alerting
**Pattern:** Set up alerts for deployment issues.

```yaml
# Prometheus alerting rules
groups:
- name: deployment
  rules:
  - alert: DeploymentFailed
    expr: kube_deployment_status_replicas_unavailable > 0
    for: 5m
    labels:
      severity: critical
    annotations:
      summary: "Deployment has unavailable replicas"
      description: "{{ $labels.deployment }} has {{ $value }} unavailable replicas"

  - alert: HighErrorRate
    expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.1
    for: 2m
    labels:
      severity: warning
    annotations:
      summary: "High error rate detected"
      description: "Error rate is {{ $value }} errors per second"
```

## AI Assistant Deployment Guidelines

### 1. When Implementing Deployments
Always include:
- [ ] Automated CI/CD pipeline with quality gates
- [ ] Environment-specific configuration management
- [ ] Database migration automation
- [ ] Health checks and monitoring
- [ ] Rollback procedures and documentation

### 2. When Configuring Infrastructure
Ensure:
- [ ] Infrastructure as Code (IaC) for reproducibility
- [ ] Security scanning and vulnerability management
- [ ] Resource limits and scaling policies
- [ ] Backup and disaster recovery procedures
- [ ] Network security and access controls

### 3. Deployment Checklist
Before each deployment:
- [ ] All tests pass in CI/CD pipeline
- [ ] Security scans complete without critical issues
- [ ] Database migrations tested in staging
- [ ] Rollback plan documented and tested
- [ ] Monitoring and alerting configured

## Quick Reference

**Automate Everything:** CI/CD pipeline handles all deployment steps
**Environment Parity:** Same containers and configurations across environments
**Blue-Green Deploy:** Zero-downtime deployments with instant rollback
**Health Checks:** Comprehensive monitoring of application and dependencies
**Security First:** Secrets management, image scanning, access controls
**Monitor Always:** Real-time alerts for deployment and application issues

For detailed implementation examples, see [`QUICK_REFERENCE_CARDS.md`](QUICK_REFERENCE_CARDS.md). 