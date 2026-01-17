# Git Workflow - GitHub Copilot Instructions

## Overview

This workflow provides a complete Git workflow from reviewing uncommitted changes through creating and pushing a pull request. It ensures code quality, test validation, and deployment readiness before committing code.

## When to Use

**Prompt GitHub Copilot with:**
- "Execute git workflow to commit and push my changes to feature/auth branch"
- "Follow git workflow to create PR for main branch with proper review"
- "Use git workflow to prepare and push changes to develop"
- "Apply git workflow for committing this feature to staging branch"

## Workflow Phases

### Phase 1: Pre-Commit Review

**Prompt GitHub Copilot:**
"Review my uncommitted changes for code quality. Check for code smells, potential bugs, security issues, and adherence to best practices before committing."

Copilot will review:
- **Code quality**: Readability, maintainability, adherence to standards
- **Potential bugs**: Logic errors, null pointer risks, edge cases
- **Security issues**: Injection vulnerabilities, exposed secrets
- **Best practices**: Language-specific conventions, patterns
- **Formatting**: Code style consistency
- **Documentation**: Missing comments, outdated docs

### Phase 2: Test Validation

**Prompt GitHub Copilot:**
"Ensure all tests pass before committing. Run the test suite and verify there are no failures or regressions."

Copilot will guide you to:
- **Run test suite**: Execute all unit and integration tests
- **Check coverage**: Verify coverage hasn't decreased
- **Fix failures**: Address any failing tests
- **Add missing tests**: Identify untested code paths
- **Performance tests**: Run if applicable
- **Linting**: Ensure code passes linting rules

### Phase 3: Deployment Readiness Check

**Prompt GitHub Copilot:**
"Verify deployment readiness for these changes. Check for breaking changes, database migrations, configuration updates, and deployment dependencies."

Copilot will verify:
- **Breaking changes**: API compatibility, backward compatibility
- **Database migrations**: Required schema changes
- **Configuration**: New environment variables, feature flags
- **Dependencies**: New packages, version updates
- **Build process**: Ensure build succeeds
- **Deployment steps**: Special deployment considerations

### Phase 4: Commit Message Creation

**Prompt GitHub Copilot:**
"Create a conventional commit message for these changes following best practices."

Commit message format:
```
<type>(<scope>): <subject>

<body>

<footer>
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Formatting changes
- `refactor`: Code restructuring
- `perf`: Performance improvements
- `test`: Adding or updating tests
- `chore`: Maintenance tasks
- `ci`: CI/CD changes

Example:
```
feat(auth): add OAuth2 authentication

Implement OAuth2 authentication flow with Google and GitHub providers.
Includes token refresh logic and user profile synchronization.

- Add OAuth2 provider configuration
- Implement token exchange and refresh
- Add user profile mapping
- Include integration tests

Closes #123
```

### Phase 5: Push and PR Creation

**Prompt GitHub Copilot:**
"Push changes and create a pull request with proper description for merging into [target-branch]."

PR description should include:

```markdown
## Description
[Brief description of changes]

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Changes Made
- [List of changes]
- [With details]

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed

## Checklist
- [ ] Code follows project conventions
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
- [ ] No new warnings generated
- [ ] Tests added/updated
- [ ] All tests passing
- [ ] Deployment considerations documented

## Related Issues
Closes #[issue number]
Related to #[issue number]

## Screenshots (if applicable)
[Add screenshots for UI changes]

## Deployment Notes
[Any special deployment considerations]
```

## Complete Workflow Example

**Single Comprehensive Prompt:**
"Execute complete git workflow for my changes targeting the 'main' branch:
1. Review uncommitted changes for quality and security
2. Verify all tests pass
3. Check deployment readiness
4. Create conventional commit message
5. Push and create PR with proper description"

## Git Best Practices

### Branching Strategy
- **Feature branches**: `feature/description`
- **Bug fixes**: `fix/description`
- **Hotfixes**: `hotfix/description`
- **Releases**: `release/version`

### Commit Guidelines
- **Atomic commits**: One logical change per commit
- **Clear messages**: Descriptive and following conventions
- **Reference issues**: Link to issue tracker
- **Avoid mass commits**: Break large changes into smaller commits

### PR Best Practices
- **Small PRs**: Easier to review, faster to merge
- **Clear description**: What, why, how
- **Link issues**: Reference related issues
- **Request reviews**: Tag appropriate reviewers
- **CI/CD**: Ensure pipelines pass
- **Respond to feedback**: Address review comments promptly

## Pre-Push Checklist

Before pushing changes, ensure:
- [ ] Code reviewed (self or peer)
- [ ] All tests passing locally
- [ ] Linting/formatting applied
- [ ] No console.log or debug statements
- [ ] No commented-out code
- [ ] No hardcoded secrets or credentials
- [ ] Documentation updated
- [ ] Commit messages follow conventions
- [ ] Branch is up-to-date with target
- [ ] No merge conflicts

## Handling Common Scenarios

### Merge Conflicts
**Prompt:** "Help me resolve merge conflicts when merging [source] into [target]"

### Reverting Changes
**Prompt:** "Help me safely revert commit [hash] while preserving subsequent changes"

### Amending Commits
**Prompt:** "Guide me to amend the last commit to include these additional changes"

### Interactive Rebase
**Prompt:** "Help me interactively rebase to clean up commit history before pushing"

### Cherry-Picking
**Prompt:** "Guide me to cherry-pick commits [hashes] from [branch] to current branch"

## Related Patterns

- `feature-development.md` - Development workflow before git operations
- `full-review.md` - Comprehensive code review before committing
- `tdd-cycle.md` - Ensure TDD cycle complete before committing
- `ci-cd-setup.md` (tool) - Set up automated CI/CD for branches
- `code-review.md` (tool) - Automated code review assistance
