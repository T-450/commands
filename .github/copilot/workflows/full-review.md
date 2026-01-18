# Full Review Workflow - GitHub Copilot Instructions

## Overview

This workflow performs a comprehensive multi-perspective code review by examining different aspects of your codebase including code quality, security, architecture, performance, and testing. It provides actionable feedback prioritized by severity and can optionally include TDD compliance verification.

## When to Use

**Prompt GitHub Copilot with:**
- "Perform a full comprehensive review on this pull request using full-review workflow"
- "Review the authentication module across all quality dimensions"
- "Conduct full-review on the payment processing feature with TDD compliance check"
- "Analyze this codebase for code quality, security, architecture, and performance issues"

## Review Configuration

### Standard Review (Default)
Comprehensive review covering all aspects without TDD-specific checks.

### TDD-Enhanced Review
Includes all standard reviews plus TDD compliance verification:
- Verifies red-green-refactor cycle adherence
- Checks test-first implementation patterns
- Analyzes test-driven design quality

Enable by mentioning "TDD review" or "TDD compliance" in your prompt.

## Review Phases

### Phase 1: Code Quality Review

**Prompt GitHub Copilot:**
"Review code quality and maintainability for [code/module/feature]. Check for code smells, readability issues, documentation gaps, and adherence to best practices like SOLID, DRY, and clean code principles."

Focus areas:
- **Code smells**: Long methods, duplicated code, inappropriate naming
- **Readability**: Clear variable names, appropriate comments, consistent formatting
- **Documentation**: API docs, inline comments, README updates
- **Best practices**: SOLID principles, DRY, separation of concerns
- **Naming conventions**: Consistent, descriptive naming
- **Code structure**: Logical organization, appropriate abstraction levels

### Phase 2: Security Audit

**Prompt GitHub Copilot:**
"Perform security audit on [code/module/feature]. Check for vulnerabilities, OWASP Top 10 compliance, authentication and authorization issues, input validation, and data protection measures."

Focus areas:
- **Injection risks**: SQL injection, XSS, command injection
- **Authentication**: Secure password handling, session management
- **Authorization**: Proper access controls, privilege escalation risks
- **Data encryption**: Sensitive data protection, secure communication
- **Input validation**: Sanitization, validation, boundary checks
- **OWASP compliance**: Top 10 vulnerability checks
- **Dependency vulnerabilities**: Known CVEs in dependencies

### Phase 3: Architecture Review

**Prompt GitHub Copilot:**
"Review architectural design and patterns in [code/module/feature]. Evaluate service boundaries, coupling and cohesion, scalability, maintainability, and adherence to architectural principles."

Focus areas:
- **Service boundaries**: Clear separation of concerns, appropriate module division
- **Coupling**: Minimize dependencies, loose coupling between components
- **Cohesion**: High cohesion within modules, related functionality grouped
- **Design patterns**: Appropriate pattern usage, pattern implementation quality
- **Scalability**: Design supports growth, horizontal scaling capability
- **Maintainability**: Easy to modify, extend, and understand
- **Technical debt**: Identified debt, prioritization recommendations

### Phase 4: Performance Analysis

**Prompt GitHub Copilot:**
"Analyze performance characteristics of [code/module/feature]. Identify bottlenecks in response times, memory usage, database queries, and provide optimization opportunities with caching strategies."

Focus areas:
- **Response times**: API latency, rendering speed, user-perceived performance
- **Memory usage**: Memory leaks, excessive allocation, garbage collection
- **Database queries**: N+1 problems, missing indexes, slow queries
- **Caching opportunities**: What to cache, cache invalidation strategies
- **Algorithm efficiency**: Big O analysis, better algorithm suggestions
- **Resource utilization**: CPU, memory, I/O optimization
- **Scalability bottlenecks**: Performance under load

### Phase 5: Test Coverage Assessment

**Prompt GitHub Copilot:**
"Evaluate test coverage and quality for [code/module/feature]. Assess unit tests, integration tests, identify gaps in coverage, edge cases, and evaluate test maintainability."

Focus areas:
- **Coverage metrics**: Line coverage, branch coverage, statement coverage
- **Test quality**: Meaningful assertions, proper test isolation
- **Edge cases**: Boundary conditions, error scenarios, null/undefined handling
- **Test maintainability**: Clear test names, minimal duplication, good fixtures
- **Test types**: Balance of unit, integration, and e2e tests
- **Coverage gaps**: Untested code paths, missing scenarios
- **Flaky tests**: Unreliable tests, timing issues

### Phase 6: TDD Compliance Review (Optional)

**Prompt GitHub Copilot:**
"Verify TDD compliance for [code/module/feature]. Check for test-first development patterns, red-green-refactor cycle evidence, test coverage trends, test quality, and test-driven design principles."

Focus areas and metrics:
- **Test-First Verification**: Were tests written before implementation?
  - Review commit history for test-before-code evidence
  - Check timestamps of test files vs implementation files
- **Red-Green-Refactor Cycles**: Evidence of proper TDD cycles
  - Commits showing failing tests (red)
  - Commits making tests pass (green)
  - Commits refactoring with stable tests (refactor)
- **Test Coverage Trends**: Coverage growth patterns during development
  - Coverage should grow incrementally with features
  - No large coverage jumps (indicates retrofitted tests)
- **Test Granularity**: Appropriate test size and scope
  - Small, focused unit tests
  - Integration tests for component interaction
- **Refactoring Evidence**: Code improvements with test safety net
  - Code quality improvements over time
  - Tests remain stable during refactoring
- **Test Quality**: Tests that drive design, not just verify behavior
  - Tests focus on behavior, not implementation details
  - Tests are readable and maintainable
- **TDD Adherence Score**: Percentage of code developed using TDD methodology
- **Cycle Completeness**: Percentage of complete red-green-refactor cycles

## Consolidated Report Structure

GitHub Copilot will provide feedback organized by priority:

### Critical Issues (Must Fix)
- Security vulnerabilities that could be exploited
- Broken functionality or severe bugs
- Architectural flaws that prevent scalability
- Data loss or corruption risks

### Recommendations (Should Fix)
- Performance bottlenecks affecting user experience
- Code quality issues that hinder maintainability
- Missing or insufficient test coverage
- Security concerns with moderate risk
- Architecture improvements for better design

### Suggestions (Nice to Have)
- Refactoring opportunities for cleaner code
- Documentation improvements
- Additional test scenarios for edge cases
- Performance micro-optimizations
- Code style consistency improvements

### Positive Feedback
- Well-implemented patterns to maintain
- Good security practices to replicate
- Excellent test coverage in certain areas
- Strong architectural decisions
- Performance optimizations done well

### TDD-Specific Report Section (When TDD Review Enabled)

Additional metrics when TDD compliance is checked:
- **TDD Adherence Score**: X% of code follows TDD methodology
- **Test-First Evidence**: Commits with test-before-implementation
- **Cycle Completeness**: Y% complete red-green-refactor cycles
- **Test Design Quality**: How well tests drive the design (1-10 scale)
- **Coverage Delta Analysis**: Coverage correlation with features
- **Refactoring Frequency**: Evidence of continuous improvement
- **Test Execution Time**: Performance of test suite
- **Test Stability**: Flakiness and reliability metrics

## Review Options

When prompting Copilot, you can specify:
- **TDD review**: Enable TDD compliance checking
- **Strict TDD**: Fail review if TDD practices not followed rigorously
- **TDD metrics**: Generate detailed TDD metrics report
- **Test-first only**: Only review code with test-first evidence
- **Focus area**: Specify one review aspect (e.g., "security audit only")
- **Severity threshold**: Only report issues above certain severity

## Best Practices

1. **Run reviews regularly**: On PRs, before releases, during refactoring
2. **Address critical issues first**: Prioritize by severity
3. **Document decisions**: Explain why certain feedback is not addressed
4. **Iterative improvement**: Don't try to fix everything at once
5. **Learn from feedback**: Use reviews to improve coding practices
6. **Automate what you can**: Integrate linters, security scanners in CI/CD

## Related Patterns

- `feature-development.md` - Development workflow that can be reviewed
- `security-hardening.md` - Deep-dive security improvements
- `performance-optimization.md` - Performance-specific optimization
- `tdd-cycle.md` - Test-driven development methodology
- `smart-fix.md` - Fix issues found during review
- `code-quality-check.md` (tool) - Automated code quality checks
- `security-scan.md` (tool) - Automated security scanning
