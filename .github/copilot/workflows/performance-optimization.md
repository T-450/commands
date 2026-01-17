# Performance Optimization Workflow - GitHub Copilot Instructions

## Overview

This workflow systematically optimizes application performance across the entire stack—from database queries to frontend rendering, infrastructure to deployment processes. It identifies bottlenecks, implements optimizations, and validates improvements with metrics.

## When to Use

**Prompt GitHub Copilot with:**
- "Optimize performance across the entire application stack using performance-optimization workflow"
- "Apply performance-optimization to fix slow API response times"
- "Use performance-optimization workflow for frontend bundle size and loading issues"
- "Optimize database and backend performance for high-traffic application"

## Workflow Phases

### Phase 1: Performance Analysis

#### Step 1: Application Profiling

**Prompt GitHub Copilot:**
"Profile application performance for [application/feature]. Identify CPU bottlenecks, memory leaks, I/O issues using profiling tools. Generate flame graphs, memory profiles, and resource utilization metrics. Prioritize optimization opportunities by impact."

Profiling approach:

**Backend Profiling**:
```python
# Python: py-spy for CPU profiling
# Generate flame graph
py-spy record -o profile.svg --pid <PID>

# Python: memory_profiler
from memory_profiler import profile

@profile
def expensive_function():
    large_list = [i for i in range(10000000)]
    return sum(large_list)
```

**Node.js Profiling**:
```bash
# Clinic.js for comprehensive profiling
clinic doctor -- node app.js
clinic flame -- node app.js

# 0x for flame graphs
0x app.js
```

**Analyzing Results**:
- Identify hot paths (functions consuming most CPU)
- Find memory leaks (growing memory over time)
- Detect I/O bottlenecks (waiting on disk/network)
- Measure garbage collection pressure

#### Step 2: Database Performance Analysis

**Prompt GitHub Copilot:**
"Analyze database performance for [application]. Review query execution plans with EXPLAIN ANALYZE, identify slow queries, check missing indexes, analyze connection pool utilization, and detect N+1 query problems. Provide query optimization report."

Database analysis:

**Query Performance**:
```sql
-- PostgreSQL: Find slow queries
SELECT 
    query,
    mean_exec_time,
    calls,
    total_exec_time
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 20;

-- Analyze specific query
EXPLAIN (ANALYZE, BUFFERS) 
SELECT * FROM orders 
WHERE user_id = 123 
AND created_at > '2024-01-01';

-- Check for sequential scans
SELECT schemaname, tablename, seq_scan, seq_tup_read
FROM pg_stat_user_tables
WHERE seq_scan > 0
ORDER BY seq_tup_read DESC;
```

**Index Analysis**:
```sql
-- Find missing indexes
SELECT 
    schemaname,
    tablename,
    attname,
    n_distinct,
    correlation
FROM pg_stats
WHERE schemaname = 'public'
AND n_distinct > 100
ORDER BY n_distinct DESC;

-- Check index usage
SELECT 
    schemaname,
    tablename,
    indexname,
    idx_scan,
    idx_tup_read,
    idx_tup_fetch
FROM pg_stat_user_indexes
ORDER BY idx_scan;
```

### Phase 2: Backend Optimization

#### Step 3: Backend Code Optimization

**Prompt GitHub Copilot:**
"Optimize backend code for [feature/endpoint] based on profiling results: [paste findings]. Focus on algorithm efficiency (better Big O), caching strategies, async/await for I/O operations, database query optimization, and removing unnecessary work."

Code optimizations:

**Algorithm Optimization**:
```python
# Before: O(n²) complexity
def find_duplicates_slow(items):
    duplicates = []
    for i in range(len(items)):
        for j in range(i + 1, len(items)):
            if items[i] == items[j]:
                duplicates.append(items[i])
    return duplicates

# After: O(n) complexity
def find_duplicates_fast(items):
    seen = set()
    duplicates = set()
    for item in items:
        if item in seen:
            duplicates.add(item)
        seen.add(item)
    return list(duplicates)
```

**Caching Strategy**:
```python
from functools import lru_cache
import redis

# In-memory cache for frequently accessed data
@lru_cache(maxsize=1000)
def get_user_permissions(user_id: str) -> List[str]:
    return db.query("SELECT permission FROM user_permissions WHERE user_id = ?", user_id)

# Redis for distributed caching
class CacheService:
    def __init__(self):
        self.redis = redis.Redis(host='localhost', port=6379)
        self.ttl = 300  # 5 minutes
    
    def get_or_compute(self, key: str, compute_fn):
        # Try cache first
        cached = self.redis.get(key)
        if cached:
            return json.loads(cached)
        
        # Compute and cache
        result = compute_fn()
        self.redis.setex(key, self.ttl, json.dumps(result))
        return result
```

**Async Optimization**:
```python
# Before: Synchronous, blocks on I/O
def get_user_data(user_id):
    profile = requests.get(f"/api/users/{user_id}")
    orders = requests.get(f"/api/users/{user_id}/orders")
    preferences = requests.get(f"/api/users/{user_id}/preferences")
    return {
        "profile": profile.json(),
        "orders": orders.json(),
        "preferences": preferences.json()
    }

# After: Async, parallel requests
import asyncio
import aiohttp

async def get_user_data(user_id):
    async with aiohttp.ClientSession() as session:
        # Execute in parallel
        profile_task = session.get(f"/api/users/{user_id}")
        orders_task = session.get(f"/api/users/{user_id}/orders")
        preferences_task = session.get(f"/api/users/{user_id}/preferences")
        
        profile, orders, preferences = await asyncio.gather(
            profile_task, orders_task, preferences_task
        )
        
        return {
            "profile": await profile.json(),
            "orders": await orders.json(),
            "preferences": await preferences.json()
        }
```

#### Step 4: API Optimization

**Prompt GitHub Copilot:**
"Optimize API design and implementation for [API/endpoints]. Implement pagination for large result sets, add response compression (gzip), enable field filtering (GraphQL/sparse fieldsets), support batch operations, and optimize serialization."

API optimizations:

**Pagination**:
```python
from fastapi import FastAPI, Query
from typing import List, Optional

@app.get("/api/users")
async def list_users(
    page: int = Query(1, ge=1),
    page_size: int = Query(20, ge=1, le=100),
    sort_by: Optional[str] = None
):
    offset = (page - 1) * page_size
    
    # Efficient pagination with total count
    users_query = db.query(User).offset(offset).limit(page_size)
    if sort_by:
        users_query = users_query.order_by(sort_by)
    
    users = users_query.all()
    total = db.query(User).count()
    
    return {
        "data": users,
        "pagination": {
            "page": page,
            "page_size": page_size,
            "total": total,
            "pages": (total + page_size - 1) // page_size
        }
    }
```

**Response Compression**:
```python
from fastapi import FastAPI
from fastapi.middleware.gzip import GZipMiddleware

app = FastAPI()
app.add_middleware(GZipMiddleware, minimum_size=1000)
```

**Field Filtering** (GraphQL-style):
```python
@app.get("/api/users/{user_id}")
async def get_user(
    user_id: int,
    fields: Optional[str] = Query(None)  # ?fields=id,name,email
):
    user = db.query(User).get(user_id)
    
    if fields:
        # Only return requested fields
        field_list = fields.split(',')
        return {field: getattr(user, field) for field in field_list}
    
    return user
```

### Phase 3: Frontend Optimization

#### Step 5: Frontend Performance

**Prompt GitHub Copilot:**
"Optimize frontend performance for [application]. Reduce bundle size with code splitting and tree shaking, implement lazy loading for routes and components, optimize images, improve Core Web Vitals (LCP, FID, CLS), and add service worker for caching."

Frontend optimizations:

**Code Splitting**:
```javascript
// React: Route-based code splitting
import { lazy, Suspense } from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

const Home = lazy(() => import('./pages/Home'));
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Profile = lazy(() => import('./pages/Profile'));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<Loading />}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/profile" element={<Profile />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

**Image Optimization**:
```javascript
// Next.js Image component with automatic optimization
import Image from 'next/image';

function ProductImage({ src, alt }) {
  return (
    <Image
      src={src}
      alt={alt}
      width={800}
      height={600}
      loading="lazy"
      placeholder="blur"
      quality={85}
      sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
    />
  );
}
```

**Bundle Size Optimization**:
```javascript
// webpack.config.js
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendor: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
          priority: 10
        },
        common: {
          minChunks: 2,
          priority: 5,
          reuseExistingChunk: true
        }
      }
    },
    minimize: true,
    usedExports: true  // Tree shaking
  }
};
```

**Performance Monitoring**:
```javascript
// Core Web Vitals tracking
import { getCLS, getFID, getLCP } from 'web-vitals';

function sendToAnalytics(metric) {
  const body = JSON.stringify(metric);
  navigator.sendBeacon('/analytics', body);
}

getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getLCP(sendToAnalytics);
```

#### Step 6: Mobile App Optimization

**Prompt GitHub Copilot:**
"Optimize mobile app performance for [app]. Reduce app startup time, minimize memory usage, optimize battery consumption, implement efficient offline storage, reduce app bundle size, and optimize image/asset loading."

Mobile optimizations:

**React Native Optimization**:
```javascript
// Optimize list rendering
import { FlatList } from 'react-native';

function OptimizedList({ data }) {
  return (
    <FlatList
      data={data}
      renderItem={({ item }) => <ListItem item={item} />}
      keyExtractor={item => item.id}
      // Performance optimizations
      initialNumToRender={10}
      maxToRenderPerBatch={10}
      windowSize={5}
      removeClippedSubviews={true}
      getItemLayout={(data, index) => ({
        length: ITEM_HEIGHT,
        offset: ITEM_HEIGHT * index,
        index
      })}
    />
  );
}

// Memoize components
import React, { memo } from 'react';

const ListItem = memo(({ item }) => (
  <View>
    <Text>{item.title}</Text>
  </View>
), (prevProps, nextProps) => {
  return prevProps.item.id === nextProps.item.id;
});
```

### Phase 4: Infrastructure Optimization

#### Step 7: Cloud Infrastructure Optimization

**Prompt GitHub Copilot:**
"Optimize cloud infrastructure for [application]. Review auto-scaling policies, right-size instance types, implement CDN for static assets, optimize database instance size, enable caching layers (CloudFront/Redis), and improve geographic distribution."

Infrastructure optimizations:

**Auto-scaling Configuration**:
```yaml
# Kubernetes HPA
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 3
  maxReplicas: 20
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 50
        periodSeconds: 60
    scaleUp:
      stabilizationWindowSeconds: 0
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
```

**CDN Configuration**:
```javascript
// CloudFront distribution for static assets
const distribution = new cloudfront.Distribution(this, 'Distribution', {
  defaultBehavior: {
    origin: new origins.S3Origin(bucket),
    compress: true,
    viewerProtocolPolicy: cloudfront.ViewerProtocolPolicy.REDIRECT_TO_HTTPS,
    cachePolicy: new cloudfront.CachePolicy(this, 'CachePolicy', {
      minTtl: Duration.seconds(0),
      defaultTtl: Duration.days(1),
      maxTtl: Duration.days(365),
      enableAcceptEncodingGzip: true,
      enableAcceptEncodingBrotli: true
    })
  }
});
```

#### Step 8: Deployment Optimization

**Prompt GitHub Copilot:**
"Optimize deployment and build processes for [application]. Improve CI/CD pipeline speed with parallel jobs and caching, optimize Docker image builds with multi-stage builds, reduce image size, and implement faster deployment strategies."

Build optimizations:

**Optimized Dockerfile**:
```dockerfile
# Multi-stage build for smaller images
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

# Production image
FROM node:18-alpine

WORKDIR /app

# Copy only necessary files
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./

# Use non-root user
RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001
USER nodejs

EXPOSE 3000
CMD ["node", "dist/server.js"]
```

**CI/CD Caching**:
```yaml
# GitHub Actions with caching
- name: Cache dependencies
  uses: actions/cache@v3
  with:
    path: |
      ~/.npm
      node_modules
      .next/cache
    key: ${{ runner.os }}-npm-${{ hashFiles('**/package-lock.json') }}
    
- name: Build with cache
  run: npm run build
  env:
    NEXT_TELEMETRY_DISABLED: 1
```

### Phase 5: Monitoring and Validation

#### Step 9: Performance Monitoring Setup

**Prompt GitHub Copilot:**
"Set up comprehensive performance monitoring for [application]. Implement Application Performance Monitoring (APM) with New Relic/DataDog, real user monitoring (RUM), custom performance metrics, and create dashboards with SLO definitions."

Monitoring setup:

**Custom Metrics**:
```python
from prometheus_client import Counter, Histogram, Gauge
import time

# Request duration histogram
request_duration = Histogram(
    'http_request_duration_seconds',
    'HTTP request duration',
    ['method', 'endpoint', 'status']
)

# Active requests gauge
active_requests = Gauge(
    'http_requests_active',
    'Number of active HTTP requests'
)

@app.middleware("http")
async def monitor_requests(request, call_next):
    active_requests.inc()
    start_time = time.time()
    
    try:
        response = await call_next(request)
        duration = time.time() - start_time
        
        request_duration.labels(
            method=request.method,
            endpoint=request.url.path,
            status=response.status_code
        ).observe(duration)
        
        return response
    finally:
        active_requests.dec()
```

**Performance SLOs**:
```yaml
# Example SLO definitions
slos:
  - name: API Response Time
    target: 95  # 95% of requests
    threshold: 200ms  # Complete in < 200ms
    
  - name: Error Rate
    target: 99.9  # 99.9% success
    threshold: 0.1%  # < 0.1% errors
    
  - name: Availability
    target: 99.95  # 99.95% uptime
    window: 30d
```

#### Step 10: Performance Testing

**Prompt GitHub Copilot:**
"Create performance test suites for [application]. Include load tests with k6/Locust, stress tests to find breaking points, endurance tests for memory leaks, and performance regression tests for CI/CD."

Performance testing:

**Load Testing with k6**:
```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 100 },  // Ramp up
    { duration: '5m', target: 100 },  // Stay at 100 users
    { duration: '2m', target: 200 },  // Ramp to 200 users
    { duration: '5m', target: 200 },  // Stay at 200 users
    { duration: '2m', target: 0 },    // Ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<200'],  // 95% under 200ms
    http_req_failed: ['rate<0.01'],    // < 1% errors
  },
};

export default function () {
  const response = http.get('https://api.example.com/users');
  
  check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 200ms': (r) => r.timings.duration < 200,
  });
  
  sleep(1);
}
```

## Performance Targets

- **API Response Time**: p95 < 200ms, p99 < 500ms
- **Database Queries**: < 100ms for most queries
- **Frontend Load Time**: FCP < 1.5s, LCP < 2.5s, TTI < 3.5s
- **Mobile App Startup**: < 2s cold start
- **Error Rate**: < 0.1%
- **Throughput**: Handle peak load + 50% headroom

## Best Practices

- **Measure First**: Always profile before optimizing
- **Set Benchmarks**: Establish baseline metrics
- **Optimize Incrementally**: One change at a time
- **Validate Impact**: Measure improvement after each change
- **Document Changes**: Record what was optimized and why
- **Monitor Production**: Continuous performance monitoring
- **Performance Budget**: Set and enforce limits

## Related Patterns

- `smart-fix.md` - Fix performance issues
- `incident-response.md` - Handle performance incidents
- `data-driven-feature.md` - Optimize data pipelines
- `database-optimizer.md` (tool) - Database optimization
- `frontend-performance.md` (tool) - Frontend tuning
- `load-testing.md` (tool) - Performance testing
- `monitoring-setup.md` (tool) - Monitoring configuration
