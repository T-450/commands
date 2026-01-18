# Incident Response Workflow - GitHub Copilot Instructions

## Overview

This workflow provides a structured approach to responding to production incidents with urgency and precision. It coordinates immediate response, root cause analysis, resolution implementation, and post-incident activities to minimize downtime and prevent recurrence.

## When to Use

**Prompt GitHub Copilot with:**
- "URGENT: Production down - follow incident-response workflow for [issue description]"
- "Apply incident-response to this database outage: [symptoms]"
- "Use incident-response workflow for this security breach: [details]"
- "Execute incident-response for performance degradation: [metrics]"

## Workflow Phases

### Phase 1: Immediate Response (Time-Critical)

#### Step 1: Incident Assessment

**Prompt GitHub Copilot:**
"URGENT: Assess production incident: [detailed description of symptoms, error messages, affected users]. Determine severity (P0/P1/P2), impact scope (users affected, services down), immediate mitigation steps, and communication plan. Time is critical."

Severity classification:

**P0 - Critical (All hands on deck)**:
- Complete service outage
- Data breach or security incident
- Data loss or corruption
- Revenue-critical feature down
- Response time: Immediate
- Update frequency: Every 15 minutes

**P1 - High (Urgent)**:
- Major feature degraded
- Significant user impact
- Performance severely degraded
- Security vulnerability exposed
- Response time: Within 30 minutes
- Update frequency: Every hour

**P2 - Medium**:
- Minor feature issues
- Limited user impact
- Workaround available
- Response time: Within 2 hours
- Update frequency: Every 4 hours

Immediate assessment:
- **What's broken**: Specific symptoms
- **Since when**: When did it start?
- **Impact**: How many users/services affected?
- **Recent changes**: Any deployments, config changes?
- **Immediate mitigation**: Can we rollback or failover?

#### Step 2: Initial Troubleshooting

**Prompt GitHub Copilot:**
"Investigate production issue: [description]. Check application logs, error rates, system metrics, recent deployments, infrastructure health, and database performance. Identify potential root causes quickly. Focus on high-probability causes first."

Investigation checklist:

**Application Logs**:
```bash
# Check recent errors
kubectl logs -n production deployment/myapp --tail=1000 | grep ERROR

# Check specific time range
kubectl logs -n production deployment/myapp --since=30m | grep -A 5 -B 5 "error\|exception\|failed"

# Check multiple pods
kubectl logs -n production -l app=myapp --tail=100 --all-containers=true
```

**Metrics and Monitoring**:
- Error rate spike?
- Response time increase?
- CPU/Memory/Disk usage?
- Database connections?
- Queue depth?
- Traffic patterns?

**Recent Changes**:
```bash
# Check recent deployments
kubectl rollout history deployment/myapp -n production

# Check recent config changes
git log --since="2 hours ago" --oneline config/

# Check infrastructure changes
terraform show
```

**Health Checks**:
- Service health endpoints
- Database connectivity
- External dependencies
- Cache availability
- Message queue status

### Phase 2: Root Cause Analysis

#### Step 3: Deep Debugging

**Prompt GitHub Copilot:**
"Debug production issue: [description] using findings from initial investigation: [paste findings]. Analyze stack traces, reproduce issue in staging if possible, identify exact root cause. Use profiling tools if needed. Provide detailed root cause analysis."

Debugging techniques:

**Stack Trace Analysis**:
```python
# Example: Analyzing error patterns
import re
from collections import Counter

def analyze_errors(log_file):
    """Analyze error patterns in logs"""
    errors = []
    with open(log_file) as f:
        for line in f:
            if 'ERROR' in line or 'EXCEPTION' in line:
                # Extract error type
                match = re.search(r'(\w+Error|\w+Exception)', line)
                if match:
                    errors.append(match.group(1))
    
    # Find most common errors
    error_counts = Counter(errors)
    return error_counts.most_common(10)
```

**Performance Profiling**:
- APM tools (New Relic, DataDog)
- Python: py-spy, cProfile
- Node.js: clinic.js, 0x
- Go: pprof
- Database: Query analyzer, slow query log

**Reproduction**:
- Recreate in staging/dev
- Minimal reproduction case
- Identify trigger conditions
- Document steps to reproduce

#### Step 4: Performance Analysis (If Applicable)

**Prompt GitHub Copilot:**
"Analyze performance aspects of incident: [description]. Check for resource exhaustion (CPU, memory, disk), bottlenecks in application code, database query performance, network latency, and external service issues. Provide performance metrics and bottleneck identification."

Performance investigation:

**Resource Monitoring**:
```bash
# Check pod resource usage
kubectl top pods -n production

# Check node resource usage
kubectl top nodes

# Historical metrics
kubectl get --raw /apis/metrics.k8s.io/v1beta1/namespaces/production/pods/
```

**Database Performance**:
```sql
-- PostgreSQL: Find slow queries
SELECT pid, now() - query_start AS duration, query
FROM pg_stat_activity
WHERE state = 'active'
ORDER BY duration DESC
LIMIT 10;

-- Check for locks
SELECT * FROM pg_locks WHERE NOT granted;

-- Connection count
SELECT count(*) FROM pg_stat_activity;
```

**Application Profiling**:
- Response time distribution
- Slowest endpoints
- Memory leaks
- CPU hotspots
- I/O bottlenecks

#### Step 5: Database Investigation (If Applicable)

**Prompt GitHub Copilot:**
"Investigate database-related aspects of incident: [description]. Check for deadlocks, slow queries, missing indexes, connection pool exhaustion, replication lag, data corruption. Provide database health report and query optimization recommendations."

Database checks:

**Query Performance**:
```sql
-- MySQL: Slow queries
SELECT * FROM mysql.slow_log 
ORDER BY query_time DESC 
LIMIT 20;

-- Check for missing indexes
SELECT * FROM sys.schema_unused_indexes;

-- Index usage
SELECT * FROM sys.schema_index_statistics
WHERE table_schema NOT IN ('mysql', 'performance_schema')
ORDER BY rows_selected DESC;
```

**Connection Health**:
```sql
-- Check connection pool status
SHOW STATUS LIKE 'Threads_connected';
SHOW STATUS LIKE 'Max_used_connections';
SHOW VARIABLES LIKE 'max_connections';
```

**Replication Status**:
```sql
-- Check replication lag
SHOW SLAVE STATUS\G
```

### Phase 3: Resolution Implementation

#### Step 6: Fix Development

**Prompt GitHub Copilot:**
"Design and implement fix for incident: [description] based on root cause: [paste root cause analysis]. Ensure fix is safe for immediate production deployment, include rollback plan, test in staging first if time permits. Provide fix implementation, safety analysis, and deployment strategy."

Fix strategies:

**Immediate Mitigation** (if needed):
- Rollback to previous version
- Increase resources (scale up/out)
- Disable problematic feature
- Switch to backup system
- Apply circuit breaker
- Rate limiting

**Permanent Fix**:
```python
# Example: Fix for memory leak
# Before: Memory leak in cache
class DataCache:
    def __init__(self):
        self.cache = {}  # Never cleared!
    
    def get(self, key):
        if key not in self.cache:
            self.cache[key] = fetch_data(key)
        return self.cache[key]

# After: Fixed with TTL and size limit
from cachetools import TTLCache

class DataCache:
    def __init__(self):
        # Max 10000 items, 1 hour TTL
        self.cache = TTLCache(maxsize=10000, ttl=3600)
    
    def get(self, key):
        if key not in self.cache:
            self.cache[key] = fetch_data(key)
        return self.cache[key]
```

**Safety Checks**:
- Unit tests passing
- Integration tests passing
- Tested in staging
- Rollback plan ready
- Monitoring in place
- Gradual rollout plan

#### Step 7: Emergency Deployment

**Prompt GitHub Copilot:**
"Deploy emergency fix for incident: [description]. Implement with minimal risk using canary/blue-green deployment, include comprehensive rollback plan, monitor deployment closely with automated health checks and rollback triggers. Provide deployment commands and monitoring dashboard."

Deployment strategies:

**Canary Deployment**:
```bash
# Deploy to 10% of traffic
kubectl set image deployment/myapp myapp=myapp:fix-v1 -n production
kubectl rollout pause deployment/myapp -n production

# Monitor metrics for 10 minutes
# If healthy, continue rollout
kubectl rollout resume deployment/myapp -n production

# If issues, rollback immediately
kubectl rollout undo deployment/myapp -n production
```

**Blue-Green Deployment**:
```bash
# Deploy new version (green)
kubectl apply -f deployment-green.yaml

# Wait for green to be healthy
kubectl wait --for=condition=ready pod -l version=green -n production --timeout=300s

# Switch traffic
kubectl patch service myapp -p '{"spec":{"selector":{"version":"green"}}}' -n production

# Keep blue running for 30 minutes for easy rollback
# If issues, switch back to blue
kubectl patch service myapp -p '{"spec":{"selector":{"version":"blue"}}}' -n production
```

**Monitoring During Deployment**:
- Error rate
- Response time
- CPU/Memory usage
- User-facing metrics
- Health check status

### Phase 4: Stabilization and Prevention

#### Step 8: System Stabilization

**Prompt GitHub Copilot:**
"Stabilize system after incident fix: [description]. Monitor system health, clear any backlogs (message queues, jobs), verify all services recovered, check data consistency, ensure full recovery across all regions. Provide system health report and recovery metrics."

Stabilization tasks:

**Health Verification**:
```bash
# Check all services healthy
kubectl get pods -n production
kubectl get deployments -n production

# Verify endpoints responding
curl -f https://api.example.com/health
curl -f https://api.example.com/metrics

# Check error rates
curl -s https://api.example.com/metrics | grep error_rate
```

**Backlog Processing**:
- Clear message queue backlogs
- Process delayed jobs
- Re-sync data if needed
- Verify no data loss

**Data Consistency**:
```sql
-- Verify data integrity
SELECT COUNT(*) FROM orders WHERE status = 'pending' AND created_at < NOW() - INTERVAL '1 hour';

-- Check for orphaned records
SELECT COUNT(*) FROM order_items oi 
LEFT JOIN orders o ON oi.order_id = o.id 
WHERE o.id IS NULL;
```

#### Step 9: Security Review (If Applicable)

**Prompt GitHub Copilot:**
"Review security implications of incident: [description]. Check for any security breaches, data exposure, credential compromise, vulnerabilities exploited. Provide security assessment, breach analysis, and hardening recommendations."

Security assessment:
- Was this a security incident?
- Was data accessed inappropriately?
- Were credentials exposed?
- Were vulnerabilities exploited?
- What security improvements needed?

### Phase 5: Post-Incident Activities

#### Step 10: Monitoring Enhancement

**Prompt GitHub Copilot:**
"Enhance monitoring to prevent recurrence of: [incident description]. Add alerts for early warning signs, improve observability with new metrics and dashboards, set up proactive health checks. Provide new monitoring rules, alert configurations, and observability improvements."

Monitoring improvements:

**New Alerts**:
```yaml
# Prometheus alert rules
groups:
  - name: incident_prevention
    rules:
      - alert: MemoryUsageHigh
        expr: container_memory_usage_bytes / container_spec_memory_limit_bytes > 0.85
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "Memory usage above 85% for 5 minutes"
          
      - alert: ErrorRateHigh
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Error rate above 5% for 2 minutes"
```

**Enhanced Dashboards**:
- Incident-specific metrics
- Leading indicators
- Dependency health
- Resource utilization trends

#### Step 11: Test Coverage

**Prompt GitHub Copilot:**
"Create tests to prevent regression of incident: [description]. Include unit tests for the fix, integration tests for the failure scenario, chaos engineering tests to verify resilience, and load tests to ensure capacity. Provide test implementations and CI/CD integration."

Regression prevention tests:

```python
# Unit test for the fix
def test_memory_leak_fixed():
    """Verify cache doesn't grow unbounded"""
    cache = DataCache()
    
    # Add many items
    for i in range(20000):
        cache.get(f"key_{i}")
    
    # Verify cache is bounded
    assert len(cache.cache) <= 10000

# Integration test for failure scenario
async def test_database_connection_recovery():
    """Verify system recovers from DB connection loss"""
    # Simulate DB connection loss
    await db.disconnect()
    
    # Make request (should handle gracefully)
    response = await client.get("/api/users")
    assert response.status_code == 503  # Service Unavailable
    
    # Reconnect DB
    await db.connect()
    
    # Verify recovery
    response = await client.get("/api/users")
    assert response.status_code == 200

# Chaos test
def test_pod_failure_resilience():
    """Verify system handles pod failures"""
    # Kill random pod
    kubectl_delete_pod("myapp-xyz")
    
    # Verify service still available
    response = requests.get("https://api.example.com/health")
    assert response.status_code == 200
```

#### Step 12: Post-Incident Documentation

**Prompt GitHub Copilot:**
"Document incident postmortem for: [incident description]. Include timeline of events, root cause analysis, impact assessment, resolution steps, lessons learned, and action items for improvement. Focus on blameless analysis and system improvements, not individual blame."

Postmortem template:

```markdown
# Incident Postmortem: [Title]

## Incident Summary
- **Date**: 2024-01-15
- **Duration**: 2 hours 15 minutes
- **Severity**: P0
- **Impact**: Complete service outage, 10,000 users affected
- **Root Cause**: Memory leak in caching layer

## Timeline (All times UTC)
- 14:00 - First alerts: High memory usage
- 14:05 - Pods started OOMKilled
- 14:10 - Service completely down
- 14:15 - Incident declared, team assembled
- 14:30 - Root cause identified: unbounded cache
- 15:00 - Fix developed and tested in staging
- 15:30 - Fix deployed to production (canary)
- 16:00 - Full rollout completed
- 16:15 - Service fully recovered

## Root Cause
Cache implementation had no size limit or TTL, causing memory to grow unbounded over time. When cache exceeded available memory, pods were killed by OOMKiller.

## Impact
- **Users**: 10,000 active users unable to access service
- **Revenue**: Estimated $5,000 in lost transactions
- **SLA**: Violated 99.9% uptime SLA for the month

## Resolution
Implemented TTLCache with 10,000 item limit and 1-hour TTL. Deployed using canary strategy.

## What Went Well
✅ Incident detected quickly by monitoring
✅ Team assembled within 15 minutes
✅ Root cause identified efficiently
✅ Fix developed and deployed safely
✅ Good communication throughout

## What Didn't Go Well
❌ No alerts for memory growth trend
❌ Cache implementation wasn't reviewed for resource limits
❌ No load testing revealed this issue
❌ Rollback initially failed (fixed during incident)

## Action Items
1. [ ] Add memory trend alerts (Owner: DevOps, Due: 2024-01-20)
2. [ ] Review all caching implementations (Owner: Backend, Due: 2024-01-25)
3. [ ] Implement load testing in CI/CD (Owner: QA, Due: 2024-02-01)
4. [ ] Fix rollback automation (Owner: DevOps, Due: 2024-01-18)
5. [ ] Add chaos testing for resource limits (Owner: SRE, Due: 2024-02-15)

## Lessons Learned
- Always implement resource limits in caching layers
- Memory monitoring needs trend-based alerts
- Load testing is critical for catching resource leaks
- Canary deployments saved us from longer outage
```

## Incident Communication Template

```markdown
**Internal Status Update** (Every 15 min for P0):
Status: [INVESTIGATING | IDENTIFIED | FIXING | MONITORING | RESOLVED]
Impact: [Description of user impact]
Current Action: [What we're doing now]
ETA: [Expected resolution time]
Next Update: [Time of next update]

**External Status Page** (For customer-facing incidents):
We are currently experiencing issues with [service]. Our team is actively working on a resolution. We will provide updates every 30 minutes.
```

## Coordination During Incidents

- **Incident Commander**: Leads response, makes decisions
- **Communications Lead**: Updates stakeholders
- **Technical Leads**: Debug and implement fixes
- **Customer Support**: Handle user inquiries
- **Documentation**: Records timeline and actions

## Related Patterns

- `smart-fix.md` - Quick issue resolution
- `performance-optimization.md` - Performance improvements
- `security-hardening.md` - Security incident response
- `monitor-setup.md` (tool) - Improve monitoring
- `chaos-engineering.md` (tool) - Test resilience
- `runbook.md` (tool) - Create incident runbooks
