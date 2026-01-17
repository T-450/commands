# Feature Development Workflow - GitHub Copilot Instructions

## Overview

This workflow guides you through implementing a complete feature from design to deployment using a multi-phase approach. It coordinates backend architecture, frontend implementation, testing, and deployment to ensure a cohesive feature implementation. You can choose between traditional development or Test-Driven Development (TDD) methodologies.

## When to Use

**Prompt GitHub Copilot with:**
- "Implement user authentication feature using the feature-development workflow"
- "Build a shopping cart feature following feature-development approach with TDD"
- "Create a notification system using traditional feature-development methodology"
- "Develop a payment processing feature using feature-development workflow"

## Development Mode Selection

### Option A: Traditional Development (Default)

Follow these steps sequentially to build your feature:

**Step 1: Backend Architecture Design**

**Prompt GitHub Copilot:**
"Design RESTful API and data model for [feature]. Include endpoint definitions, database schema, and service boundaries."

Key deliverables:
- API endpoint specifications
- Database schema design
- Service architecture diagram
- Data flow documentation

**Step 2: Frontend Implementation**

**Prompt GitHub Copilot:**
"Create UI components for [feature] using the following API design: [paste API design]. Ensure proper state management and API integration."

Key deliverables:
- React/Vue/Angular components
- State management setup
- API client integration
- Responsive UI implementation

**Step 3: Test Coverage**

**Prompt GitHub Copilot:**
"Write comprehensive tests for [feature] covering these backend endpoints: [list endpoints] and these frontend components: [list components]. Include unit, integration, and e2e tests."

Key deliverables:
- Unit tests with 80%+ coverage
- Integration tests for API endpoints
- End-to-end user flow tests
- Test documentation

**Step 4: Production Deployment**

**Prompt GitHub Copilot:**
"Prepare production deployment for [feature]. Include CI/CD pipeline configuration, containerization with Docker, and monitoring setup."

Key deliverables:
- CI/CD pipeline (GitHub Actions/GitLab CI)
- Docker configurations
- Monitoring and alerting setup
- Deployment documentation

### Option B: TDD-Driven Development

For test-first development, follow the red-green-refactor cycle:

**Step 1: Test-First Backend Design**

**Prompt GitHub Copilot:**
"Write failing tests for backend API [feature]. Define test cases for all expected endpoints and behaviors before implementation. Follow TDD red phase."

Key deliverables:
- Comprehensive test suite (failing)
- API contract definitions
- Test data fixtures
- Expected behavior documentation

**Step 2: Test-First Frontend Design**

**Prompt GitHub Copilot:**
"Write failing tests for frontend components [feature]. Include unit tests and integration tests for UI behavior. Follow TDD methodology."

Key deliverables:
- Component test suite (failing)
- User interaction test scenarios
- Expected UI behavior tests
- Mock API responses

**Step 3: Incremental Implementation**

**Prompt GitHub Copilot:**
"Implement [feature] to pass all tests following strict red-green-refactor cycles. Write minimal code to make each test pass, one test at a time."

Key deliverables:
- Implementation code that passes tests
- Incremental commits showing TDD cycles
- Green test suite
- Implementation notes

**Step 4: Refactoring & Optimization**

**Prompt GitHub Copilot:**
"Refactor [feature] implementation while maintaining all passing tests. Optimize for maintainability, performance, and code quality."

Key deliverables:
- Refactored, clean code
- All tests still passing
- Performance improvements
- Code quality metrics

**Step 5: Production Deployment**

**Prompt GitHub Copilot:**
"Deploy TDD-developed [feature]. Ensure all tests run in CI/CD pipeline and verify 80%+ test coverage before deployment."

Key deliverables:
- CI/CD with test gates
- Coverage reports
- Deployment pipeline
- Rollback procedures

## TDD Configuration Options

When using TDD mode, consider these parameters:

- **Minimum test coverage**: Set to 80% or higher
- **Strict TDD enforcement**: Require tests before any implementation code
- **Red-green-refactor verification**: Track commit history showing proper TDD cycles
- **Test-first evidence**: Document test creation timestamps vs. implementation

For granular TDD control, use the dedicated `tdd-cycle.md` workflow.

## Best Practices

1. **Documentation**: Document API contracts, component interfaces, and architectural decisions
2. **Code Review**: Have designs reviewed before implementation begins
3. **Incremental Delivery**: Break large features into smaller, deployable increments
4. **Security**: Include security considerations at every phase
5. **Performance**: Consider performance implications from the design phase
6. **Monitoring**: Plan monitoring and observability from the start

## Validation Checklist

Before considering the feature complete:

- [ ] API endpoints documented and tested
- [ ] Frontend components functional and accessible
- [ ] Test coverage meets minimum threshold (80%+)
- [ ] Security audit passed
- [ ] Performance benchmarks met
- [ ] CI/CD pipeline configured and tested
- [ ] Monitoring and alerting in place
- [ ] Documentation complete
- [ ] Code review approved
- [ ] Deployment procedure documented

## Related Patterns

- `tdd-cycle.md` - For granular test-driven development
- `full-stack-feature.md` - For multi-platform feature implementation
- `smart-fix.md` - For debugging issues during development
- `full-review.md` - For comprehensive code review
- `security-hardening.md` - For security-focused development
- `performance-optimization.md` - For performance-critical features
