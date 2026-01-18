# Smart Fix Workflow - GitHub Copilot Instructions

## Overview

This workflow intelligently analyzes issues and automatically routes them to the most appropriate solution strategy. It categorizes problems by domain (deployment, code errors, database, performance, legacy code) and applies specialized expertise to fix them efficiently.

## When to Use

**Prompt GitHub Copilot with:**
- "Smart fix this deployment failure: [error details]"
- "Use smart-fix to resolve this database performance issue: [description]"
- "Apply smart-fix workflow to debug this production error: [stack trace]"
- "Smart fix the legacy authentication code that's causing issues"

## How It Works

1. **Analysis Phase**: Copilot analyzes the issue description to categorize the problem domain
2. **Routing**: Based on the category, applies appropriate specialized expertise
3. **Resolution**: Provides targeted fixes specific to the problem type
4. **Verification**: Suggests validation steps to confirm the fix

## Issue Categories and Solutions

### For Deployment/Infrastructure Issues

**When you see:** Deployment failures, infrastructure problems, DevOps concerns, CI/CD pipeline errors, container issues, Kubernetes problems

**Prompt GitHub Copilot:**
"Debug and fix this deployment/infrastructure issue: [describe the issue]. Include root cause analysis, fix implementation, and prevention measures."

Expected solutions:
- **CI/CD pipeline fixes**: Correct workflow configurations, fix broken builds
- **Container issues**: Dockerfile corrections, image optimization
- **Kubernetes problems**: Resource limits, health checks, configuration fixes
- **Infrastructure**: Network issues, load balancer config, DNS problems
- **Environment**: Environment variable issues, secrets management
- **Dependency**: Missing dependencies, version conflicts

### For Code Errors and Bugs

**When you see:** Application errors, exceptions, functional bugs, runtime errors, logic errors

**Prompt GitHub Copilot:**
"Analyze and fix this code error: [error message and stack trace]. Provide root cause analysis, step-by-step debugging approach, and complete solution with tests."

Expected solutions:
- **Root cause**: Detailed analysis of what's causing the error
- **Reproduction**: Steps to reproduce the issue
- **Fix implementation**: Code changes to resolve the bug
- **Tests**: Unit/integration tests to prevent regression
- **Edge cases**: Handle related edge cases
- **Error handling**: Improved error handling and logging

### For Database Performance Issues

**When you see:** Slow queries, database bottlenecks, data access pattern issues, connection pool exhaustion, lock contention

**Prompt GitHub Copilot:**
"Optimize database performance for this issue: [describe symptoms]. Include query analysis, indexing strategies, schema improvements, and connection pool tuning."

Expected solutions:
- **Query optimization**: Rewrite slow queries, add proper indexes
- **Indexing strategy**: Analyze and add missing indexes
- **Schema improvements**: Denormalization, partitioning, archiving
- **Connection pooling**: Optimize pool size and configuration
- **Query patterns**: Fix N+1 queries, batch operations
- **Lock optimization**: Reduce lock contention, optimize transactions
- **Caching**: Query result caching, materialized views

### For Application Performance Issues

**When you see:** Slow response times, high resource usage, memory leaks, CPU bottlenecks, performance degradation

**Prompt GitHub Copilot:**
"Profile and optimize application performance for: [describe performance issue]. Identify bottlenecks with profiling, provide optimization strategies, and implement caching where beneficial."

Expected solutions:
- **Profiling results**: CPU, memory, I/O analysis
- **Bottleneck identification**: Specific slow code paths
- **Algorithm optimization**: More efficient algorithms
- **Caching strategy**: What to cache, cache invalidation
- **Async operations**: Convert blocking to non-blocking
- **Resource optimization**: Memory usage, connection reuse
- **Code optimization**: Remove unnecessary work, lazy loading

### For Legacy Code Issues

**When you see:** Outdated code, deprecated patterns, technical debt, compatibility issues, unmaintainable code

**Prompt GitHub Copilot:**
"Modernize and fix legacy code issue: [describe the problem]. Provide migration path from old to new patterns, updated implementation using modern practices, and gradual refactoring strategy."

Expected solutions:
- **Pattern migration**: From old to modern patterns
- **Dependency updates**: Upgrade deprecated dependencies
- **API modernization**: Update deprecated API usage
- **Code refactoring**: Improve structure and readability
- **Test coverage**: Add tests before refactoring
- **Incremental approach**: Safe, gradual modernization
- **Documentation**: Document old vs. new approach

## Multi-Domain Complex Issues

For issues spanning multiple domains, Copilot will:

1. **Identify primary symptom**: Categorize the main issue
2. **Analyze related aspects**: Identify secondary concerns
3. **Coordinate fixes**: Provide solutions across all affected areas
4. **Verify integration**: Ensure fixes work together properly

**Example Prompt for Complex Issues:**
"Smart fix this complex issue: [description]. The problem involves both database performance and application code errors. Coordinate fixes across all affected areas."

## Best Practices

1. **Provide context**: Include error messages, stack traces, logs
2. **Describe symptoms**: What's failing, when it fails, impact
3. **Include environment**: OS, versions, configurations
4. **Recent changes**: What changed before the issue appeared
5. **Attempted solutions**: What you've already tried

## Effective Issue Descriptions

Good issue description structure:
```
**Problem**: [Brief description]
**Symptoms**: [Observable behavior]
**Error messages**: [Exact error text]
**Context**: [When does it happen]
**Environment**: [Relevant system info]
**Recent changes**: [What changed]
**Impact**: [User/business impact]
```

## Verification Steps

After applying fixes, Copilot will suggest:
- **Unit tests**: To verify the specific fix
- **Integration tests**: To ensure system-wide compatibility
- **Performance tests**: For performance-related fixes
- **Manual testing**: Steps to manually verify
- **Monitoring**: What to watch for confirmation

## Related Patterns

- `incident-response.md` - For production emergencies
- `performance-optimization.md` - Deep performance analysis
- `legacy-modernize.md` - Comprehensive legacy code updates
- `tdd-cycle.md` - Fix with test-first approach
- `full-review.md` - Review after major fixes
- `debugger.md` (tool) - Debugging assistance
- `performance-profiler.md` (tool) - Performance profiling
