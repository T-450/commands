# GitHub Copilot Custom Instructions

Welcome to the Claude Code Commands repository converted for GitHub Copilot! This file provides global development guidance and patterns.

## Repository Purpose

This repository contains 57 production-ready development patterns (15 workflows + 42 tools) converted from Claude Code slash commands to GitHub Copilot custom instructions format.

## How to Use with GitHub Copilot

GitHub Copilot will automatically read these instructions when you:
1. Work in this repository
2. Ask Copilot for help via Chat (@workspace or inline chat)
3. Use Copilot suggestions while coding

## Command Organization

### Workflows (`copilot/workflows/`)
Multi-agent orchestration patterns for complex, cross-domain tasks:
- `feature-development.md` - End-to-end feature implementation
- `tdd-cycle.md` - Test-driven development orchestration
- `smart-fix.md` - Intelligent problem resolution
- `full-review.md` - Multi-perspective code analysis
- `security-hardening.md` - Security-first development
- `performance-optimization.md` - System-wide optimization
- `legacy-modernize.md` - Codebase modernization
- `incident-response.md` - Production issue resolution
- And more...

### Tools (`copilot/tools/`)
Focused, single-purpose utilities for specific development operations:
- **AI & ML**: `ai-assistant.md`, `ai-review.md`, `langchain-agent.md`, `prompt-optimize.md`
- **Testing**: `test-harness.md`, `tdd-red.md`, `tdd-green.md`, `tdd-refactor.md`
- **Security**: `security-scan.md`, `compliance-check.md`, `accessibility-audit.md`
- **DevOps**: `docker-optimize.md`, `k8s-manifest.md`, `monitor-setup.md`, `deploy-checklist.md`
- **Data**: `data-pipeline.md`, `data-validation.md`, `db-migrate.md`
- And 30+ more specialized tools...

## General Development Principles

### Code Quality Standards
- Write clean, maintainable code following SOLID principles
- Include comprehensive documentation and comments
- Follow language-specific best practices and conventions
- Implement proper error handling and logging
- Write code that is testable and well-structured

### Security First
- Never hardcode credentials or secrets
- Use environment variables for configuration
- Implement input validation and sanitization
- Follow OWASP Top 10 security guidelines
- Use parameterized queries to prevent SQL injection
- Implement proper authentication and authorization
- Use security headers and HTTPS

### Testing Strategy
- Write tests before implementation (TDD when applicable)
- Aim for 80%+ code coverage
- Include unit, integration, and e2e tests
- Test edge cases and error scenarios
- Keep tests fast, isolated, and independent
- Use meaningful test names that document behavior

### Performance Considerations
- Optimize for readability first, performance second
- Use appropriate data structures and algorithms
- Implement caching where beneficial
- Minimize database queries and network calls
- Profile before optimizing
- Document performance-critical sections

### Documentation Standards
- Write self-documenting code with clear names
- Include docstrings/JSDoc for functions and classes
- Document complex algorithms and business logic
- Maintain up-to-date README files
- Include code examples in documentation
- Document APIs with OpenAPI/Swagger

## Framework-Specific Guidance

### Python
- Follow PEP 8 style guide
- Use type hints for function parameters and returns
- Prefer f-strings for formatting
- Use virtual environments (venv/poetry)
- Leverage list/dict comprehensions appropriately
- Use context managers for resource handling

### JavaScript/TypeScript
- Use TypeScript for type safety
- Follow ESLint recommended rules
- Prefer const/let over var
- Use async/await over callbacks
- Implement proper error handling with try/catch
- Use modern ES6+ features

### React
- Use functional components with hooks
- Implement proper state management
- Follow component composition patterns
- Use prop-types or TypeScript for type checking
- Optimize re-renders with useMemo/useCallback
- Follow React best practices and patterns

### FastAPI/Django/Flask
- Use Pydantic models for validation (FastAPI)
- Implement proper middleware for cross-cutting concerns
- Use ORM for database operations
- Implement API versioning
- Add comprehensive error handling
- Include request/response validation

### Database
- Use migrations for schema changes
- Implement proper indexing
- Use transactions where appropriate
- Avoid N+1 query problems
- Implement connection pooling
- Use parameterized queries

## Architecture Patterns

### API Development
- Design RESTful APIs following HTTP standards
- Version your APIs (e.g., /api/v1/)
- Implement proper status codes
- Include pagination for list endpoints
- Add rate limiting and authentication
- Document with OpenAPI/Swagger

### Microservices
- Keep services loosely coupled
- Implement service discovery
- Use API gateways for routing
- Implement circuit breakers
- Add distributed tracing
- Use message queues for async communication

### Error Handling
- Use specific exception types
- Provide meaningful error messages
- Log errors with context
- Implement retry mechanisms where appropriate
- Don't expose sensitive information in errors
- Return appropriate HTTP status codes

## Deployment & DevOps

### Containerization
- Use multi-stage Docker builds
- Run as non-root user
- Keep images small and secure
- Use .dockerignore appropriately
- Pin dependency versions
- Implement health checks

### Kubernetes
- Use proper resource limits
- Implement liveness/readiness probes
- Use ConfigMaps/Secrets for configuration
- Implement network policies
- Use rolling updates
- Add monitoring and logging

### CI/CD
- Automate testing in pipelines
- Run security scans
- Implement blue-green or canary deployments
- Use infrastructure as code
- Automate dependency updates
- Include rollback procedures

## How to Ask Copilot for Help

### For Workflows (Complex Tasks)
Ask Copilot to follow specific workflow patterns:
- "Implement user authentication following the feature-development workflow"
- "Debug this performance issue using the smart-fix approach"
- "Apply TDD methodology for this shopping cart feature"

### For Tools (Specific Operations)
Request specific tool patterns:
- "Generate API scaffolding for user management"
- "Run security scan and show vulnerabilities"
- "Create comprehensive test harness for this module"
- "Generate Kubernetes manifests for this service"

### Best Practices for Prompts
1. Be specific about requirements and constraints
2. Mention the technology stack
3. Specify testing requirements
4. Include security considerations
5. Request documentation

## Getting More Specific Guidance

For detailed instructions on specific patterns, navigate to:
- `.github/copilot/workflows/` - For multi-step orchestrated workflows
- `.github/copilot/tools/` - For focused single-purpose operations

Each file contains:
- Detailed implementation guidance
- Code examples and templates
- Best practices specific to that pattern
- Integration points with other patterns
- Validation checklists

## Migration from Claude Code

If you're migrating from Claude Code slash commands:
1. Slash commands like `/api-scaffold` → Ask Copilot Chat: "Generate API scaffolding following the api-scaffold pattern"
2. Workflows like `/workflows:feature-development` → "Implement this feature following the feature-development workflow"
3. All command content is preserved in respective `.md` files for reference

## Contributing

When adding new patterns:
1. Create a markdown file in appropriate directory
2. Follow the existing structure
3. Include clear examples
4. Add validation criteria
5. Document integration points
6. Update this index

## Support

- For workflow guidance: See `.github/copilot/workflows/`
- For tool guidance: See `.github/copilot/tools/`
- For examples: Check `examples/` directory
- For issues: Use GitHub Issues
