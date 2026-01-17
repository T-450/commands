# Legacy Modernize Workflow - GitHub Copilot Instructions

## Overview

This workflow systematically modernizes legacy code by analyzing outdated patterns, creating comprehensive tests, planning modernization, implementing updates, and validating security and performance improvements. It ensures a safe, incremental approach to technical debt reduction.

## When to Use

**Prompt GitHub Copilot with:**
- "Modernize the legacy authentication module using legacy-modernize workflow"
- "Apply legacy-modernize to update this PHP 5.6 codebase to PHP 8.2"
- "Use legacy-modernize workflow to refactor this jQuery code to React"
- "Modernize the legacy database layer with modern ORM patterns"

## Workflow Phases

### Phase 1: Analysis and Planning

**Prompt GitHub Copilot:**
"Analyze this legacy code and plan modernization: [code or module description]. Identify outdated patterns, technical debt, dependencies to update, and create a prioritized modernization roadmap."

Analysis includes:
- **Code patterns**: Identify deprecated patterns, anti-patterns
- **Dependencies**: Outdated libraries, security vulnerabilities
- **Architecture**: Monolithic vs. modern architecture opportunities
- **Technology stack**: Framework versions, language versions
- **Technical debt**: Accumulated shortcuts, workarounds
- **Performance issues**: Known bottlenecks, inefficiencies
- **Security concerns**: Vulnerable patterns, insecure practices
- **Maintainability**: How difficult is it to maintain/extend?

Modernization plan should include:
- **Priority ranking**: What to modernize first
- **Risk assessment**: What could break
- **Incremental steps**: Phased approach
- **Rollback strategy**: How to revert if needed
- **Testing strategy**: How to ensure safety

### Phase 2: Create Safety Net Tests

**Prompt GitHub Copilot:**
"Create comprehensive tests for this legacy code before modernization: [code or module]. Include unit tests, integration tests, and characterization tests that capture current behavior."

Test coverage must include:
- **Characterization tests**: Document existing behavior
  - Test current outputs, even if they seem wrong
  - Capture edge cases and quirks
  - Document for comparison after modernization
- **Unit tests**: Test individual functions/methods
- **Integration tests**: Test component interactions
- **Regression tests**: Prevent unintended changes
- **Edge cases**: Boundary conditions, error scenarios
- **Performance baselines**: Current performance metrics

**Why tests first?**
- Provides safety net for refactoring
- Documents current behavior
- Catches regressions immediately
- Enables confident modernization
- Validates improvements

### Phase 3: Review Modernization Plan

**Prompt GitHub Copilot:**
"Review the modernization plan for [legacy code/module]. Validate the approach, identify risks, and suggest improvements to the modernization strategy."

Review considerations:
- **Feasibility**: Is the plan practical?
- **Risk mitigation**: Are risks properly addressed?
- **Completeness**: Does it cover all aspects?
- **Timeline**: Is the phasing reasonable?
- **Dependencies**: Are dependency updates safe?
- **Breaking changes**: How are they handled?
- **Team capacity**: Can the team execute this?

### Phase 4: Implement Modernization

**Prompt GitHub Copilot:**
"Implement modernization for [legacy code/module] using [Python/Go/JavaScript/etc.] best practices. Follow the approved modernization plan, update code incrementally, and ensure all tests continue passing."

Modernization implementation:

**Language/Framework Updates**
- Update to latest stable versions
- Replace deprecated APIs
- Use modern language features
- Follow current best practices

**Pattern Modernization**
- Replace old patterns with modern equivalents
- Implement SOLID principles
- Improve separation of concerns
- Use dependency injection
- Apply appropriate design patterns

**Code Quality Improvements**
- Add type hints/annotations
- Improve naming conventions
- Extract magic numbers to constants
- Reduce complexity
- Remove code duplication

**Example Modernizations:**

**Python 2 → Python 3**
```python
# Before (Python 2)
print "Hello"
values = dict.items()
result = value / 2  # Integer division

# After (Python 3)
print("Hello")
values = list(dict.items())
result = value // 2  # Explicit integer division
```

**Callback → Async/Await**
```javascript
// Before (Callbacks)
function fetchUser(id, callback) {
  db.query('SELECT * FROM users WHERE id = ?', [id], (err, user) => {
    if (err) return callback(err);
    callback(null, user);
  });
}

// After (Async/Await)
async function fetchUser(id) {
  try {
    const user = await db.query('SELECT * FROM users WHERE id = ?', [id]);
    return user;
  } catch (error) {
    logger.error('Failed to fetch user', { id, error });
    throw new DatabaseError('User fetch failed', { cause: error });
  }
}
```

**jQuery → React**
```javascript
// Before (jQuery)
$('#user-list').html('');
users.forEach(user => {
  $('#user-list').append(`<li>${user.name}</li>`);
});

// After (React)
function UserList({ users }) {
  return (
    <ul id="user-list">
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### Phase 5: Security Audit

**Prompt GitHub Copilot:**
"Audit security improvements after modernizing [code/module]. Verify that security vulnerabilities have been fixed, modern security practices are implemented, and no new vulnerabilities were introduced."

Security validation:
- **Vulnerability fixes**: Confirm old vulnerabilities resolved
- **New security features**: Authentication, authorization improvements
- **Input validation**: Proper sanitization and validation
- **SQL injection**: Parameterized queries, ORM usage
- **XSS prevention**: Output encoding, CSP headers
- **CSRF protection**: Tokens, SameSite cookies
- **Dependency security**: No known CVEs in new dependencies
- **Secrets management**: No hardcoded credentials
- **Security headers**: Proper HTTP security headers
- **Encryption**: Sensitive data properly encrypted

### Phase 6: Performance Validation

**Prompt GitHub Copilot:**
"Validate that modernization improved or maintained performance for [code/module]. Compare performance metrics before and after, identify any regressions, and optimize if needed."

Performance comparison:
- **Response times**: API latency, page load times
- **Throughput**: Requests per second, transactions per second
- **Resource usage**: CPU, memory, disk I/O
- **Database performance**: Query times, connection usage
- **Scalability**: Performance under load
- **Bottlenecks**: New bottlenecks introduced?

Expected outcomes:
- ✅ Performance maintained or improved
- ✅ No new bottlenecks
- ✅ Better resource efficiency
- ✅ Improved scalability

If performance regressed:
- Profile and identify cause
- Optimize critical paths
- Add caching where appropriate
- Consider alternative approaches

## Incremental Modernization Strategy

### Strangler Fig Pattern
Gradually replace old system with new:
1. Create new implementation alongside old
2. Route new traffic to new implementation
3. Gradually migrate existing functionality
4. Remove old implementation when complete

### Branch by Abstraction
Introduce abstraction layer:
1. Create abstraction over legacy code
2. Implement new version behind abstraction
3. Switch abstraction to use new version
4. Remove old implementation

### Parallel Run
Run old and new side-by-side:
1. Implement new version
2. Run both versions in parallel
3. Compare outputs for consistency
4. Switch to new version when validated
5. Remove old version

## Best Practices

1. **Test first**: Always create tests before modernizing
2. **Incremental changes**: Small, safe steps
3. **Continuous validation**: Run tests after each change
4. **Version control**: Commit frequently with clear messages
5. **Documentation**: Document why changes were made
6. **Performance monitoring**: Watch for regressions
7. **Rollback ready**: Always have a rollback plan
8. **Stakeholder communication**: Keep team informed of progress

## Common Pitfalls to Avoid

- ❌ **Big bang rewrites**: Modernize incrementally instead
- ❌ **Skipping tests**: Always test before modernizing
- ❌ **Changing behavior**: Maintain existing behavior unless intentionally changing
- ❌ **Ignoring performance**: Monitor performance throughout
- ❌ **Breaking dependencies**: Update dependencies carefully
- ❌ **Poor communication**: Keep stakeholders informed

## Modernization Checklist

Before completing modernization:
- [ ] All tests passing (old and new)
- [ ] Security audit completed
- [ ] Performance validated
- [ ] Documentation updated
- [ ] Dependencies updated
- [ ] Code review completed
- [ ] Deployment plan ready
- [ ] Rollback procedure documented
- [ ] Team trained on new code
- [ ] Monitoring configured

## Related Patterns

- `smart-fix.md` - Fix issues in legacy code
- `security-hardening.md` - Enhance security during modernization
- `performance-optimization.md` - Optimize modernized code
- `full-review.md` - Review modernization quality
- `tdd-cycle.md` - Use TDD for new implementations
- `test-harness.md` (tool) - Create comprehensive test suites
- `code-quality-check.md` (tool) - Validate code improvements
