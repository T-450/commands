# Migration Guide: Claude Code → GitHub Copilot

## Overview
This guide helps you transition from using Claude Code slash commands to GitHub Copilot custom instructions for the same 57 development patterns.

## Key Differences

### Invocation Method

**Claude Code (Old)**
```bash
/workflows:feature-development implement OAuth2 authentication
/tools:security-scan perform vulnerability assessment
/api-scaffold create user management endpoints
```

**GitHub Copilot (New)**
```
@workspace Implement OAuth2 authentication using the feature-development workflow
@workspace Run security scan to find vulnerabilities
@workspace Generate API scaffolding for user management endpoints
```

### Location of Instructions

**Claude Code**
- Global: `~/.claude/commands/`
- Workflows: `~/.claude/commands/workflows/`
- Tools: `~/.claude/commands/tools/`

**GitHub Copilot**
- Global: `.github/copilot-instructions.md`
- Workflows: `.github/copilot/workflows/`
- Tools: `.github/copilot/tools/`

## Pattern Conversion Reference

### Workflows

| Claude Code Command | GitHub Copilot Prompt |
|---------------------|----------------------|
| `/workflows:feature-development [args]` | `@workspace Implement [feature] using the feature-development workflow` |
| `/workflows:tdd-cycle [args]` | `@workspace Build [component] using TDD methodology` |
| `/workflows:smart-fix [args]` | `@workspace Debug [issue] using the smart-fix approach` |
| `/workflows:full-review [args]` | `@workspace Review this code using full-review workflow` |
| `/workflows:security-hardening [args]` | `@workspace Apply security hardening for [component]` |
| `/workflows:performance-optimization [args]` | `@workspace Optimize performance of [system]` |
| `/workflows:legacy-modernize [args]` | `@workspace Modernize this legacy code` |
| `/workflows:incident-response [args]` | `@workspace Investigate production issue: [description]` |
| `/workflows:full-stack-feature [args]` | `@workspace Implement full-stack feature: [description]` |
| `/workflows:data-driven-feature [args]` | `@workspace Build ML-powered feature: [description]` |
| `/workflows:ml-pipeline [args]` | `@workspace Create ML pipeline for [use case]` |
| `/workflows:workflow-automate [args]` | `@workspace Automate CI/CD for [project]` |
| `/workflows:multi-platform [args]` | `@workspace Develop cross-platform app for [platforms]` |
| `/workflows:git-workflow [args]` | `@workspace Set up git workflow for [team]` |
| `/workflows:improve-agent [args]` | `@workspace Optimize agent for [task]` |

### Tools

| Category | Claude Code | GitHub Copilot |
|----------|-------------|----------------|
| **AI & ML** | `/tools:ai-assistant` | `@workspace Create AI assistant for [purpose]` |
| | `/tools:ai-review` | `@workspace Review ML model code` |
| | `/tools:langchain-agent` | `@workspace Build LangChain agent for [task]` |
| | `/tools:prompt-optimize` | `@workspace Optimize this prompt` |
| **API Development** | `/tools:api-scaffold` | `@workspace Generate API scaffolding for [resource]` |
| | `/tools:api-mock` | `@workspace Create API mocks for testing` |
| **Testing** | `/tools:test-harness` | `@workspace Generate test harness for [component]` |
| | `/tools:tdd-red` | `@workspace Write failing tests for [feature]` |
| | `/tools:tdd-green` | `@workspace Implement code to pass these tests` |
| | `/tools:tdd-refactor` | `@workspace Refactor while keeping tests green` |
| **Security** | `/tools:security-scan` | `@workspace Run security scan` |
| | `/tools:compliance-check` | `@workspace Check compliance for [standard]` |
| | `/tools:accessibility-audit` | `@workspace Audit accessibility (WCAG)` |
| **DevOps** | `/tools:docker-optimize` | `@workspace Optimize Docker configuration` |
| | `/tools:k8s-manifest` | `@workspace Generate Kubernetes manifests` |
| | `/tools:monitor-setup` | `@workspace Set up monitoring` |
| | `/tools:deploy-checklist` | `@workspace Create deployment checklist` |
| **Database** | `/tools:db-migrate` | `@workspace Create database migration for [changes]` |
| | `/tools:data-pipeline` | `@workspace Design data pipeline for [use case]` |
| | `/tools:data-validation` | `@workspace Add data validation for [dataset]` |
| **Code Quality** | `/tools:code-explain` | `@workspace Explain this code` |
| | `/tools:code-migrate` | `@workspace Migrate from [old] to [new]` |
| | `/tools:refactor-clean` | `@workspace Refactor and clean this code` |
| | `/tools:tech-debt` | `@workspace Analyze technical debt` |
| **Documentation** | `/tools:doc-generate` | `@workspace Generate documentation` |
| | `/tools:pr-enhance` | `@workspace Improve this PR description` |
| | `/tools:standup-notes` | `@workspace Generate standup notes` |
| **Debugging** | `/tools:debug-trace` | `@workspace Debug and trace [issue]` |
| | `/tools:error-analysis` | `@workspace Analyze error patterns` |
| | `/tools:error-trace` | `@workspace Trace production error` |
| | `/tools:smart-debug` | `@workspace Debug intelligently: [issue]` |
| **Dependencies** | `/tools:deps-audit` | `@workspace Audit dependencies` |
| | `/tools:deps-upgrade` | `@workspace Upgrade dependencies safely` |
| **Other Tools** | `/tools:issue` | `@workspace Create issue template` |
| | `/tools:cost-optimize` | `@workspace Optimize costs for [service]` |
| | `/tools:onboard` | `@workspace Create onboarding guide` |
| | `/tools:context-save` | `@workspace Save context` |
| | `/tools:context-restore` | `@workspace Restore context` |
| | `/tools:config-validate` | `@workspace Validate configuration` |
| | `/tools:multi-agent-review` | `@workspace Multi-perspective code review` |
| | `/tools:multi-agent-optimize` | `@workspace Coordinated optimization` |
| | `/tools:slo-implement` | `@workspace Implement SLO/SLI` |

## Migration Steps

### 1. For Individual Projects

If you want to use these patterns in a specific project:

```bash
# Navigate to your project
cd /path/to/your/project

# Copy the GitHub Copilot instructions
cp -r /path/to/commands/.github/copilot* .github/

# GitHub Copilot will automatically discover them
```

### 2. For All Projects (Global Setup)

**Option A: Clone to common location**
```bash
# Clone to a common location
git clone https://github.com/T-450/commands.git ~/copilot-patterns

# Symlink to projects as needed
cd /path/to/project
ln -s ~/copilot-patterns/.github/copilot .github/copilot
ln -s ~/copilot-patterns/.github/copilot-instructions.md .github/copilot-instructions.md
```

**Option B: Submodule**
```bash
cd /path/to/project
git submodule add https://github.com/T-450/commands.git .copilot-patterns
ln -s .copilot-patterns/.github/copilot .github/copilot
ln -s .copilot-patterns/.github/copilot-instructions.md .github/copilot-instructions.md
```

### 3. Verify Installation

Open GitHub Copilot Chat and try:
```
@workspace List available workflows
@workspace What tools are available?
```

## Usage Examples

### Before (Claude Code)

```bash
# Generate an API
/api-scaffold "REST API for user management with FastAPI, PostgreSQL, and JWT auth"

# Run security scan
/security-scan "FastAPI application with authentication endpoints"

# Implement feature with TDD
/workflows:tdd-cycle "Shopping cart with discount calculation"
```

### After (GitHub Copilot)

```
# Generate an API
@workspace Generate API scaffolding for user management using FastAPI, PostgreSQL, and JWT auth

# Run security scan
@workspace Run security scan on FastAPI application focusing on authentication

# Implement feature with TDD
@workspace Implement shopping cart with discount calculation using TDD methodology
```

## Best Practices

### 1. Be Specific
❌ Bad: `@workspace Create API`
✅ Good: `@workspace Generate API scaffolding for blog posts using FastAPI with PostgreSQL`

### 2. Reference Patterns
❌ Bad: `@workspace Make this code better`
✅ Good: `@workspace Refactor this code using the refactor-clean pattern`

### 3. Provide Context
❌ Bad: `@workspace Add tests`
✅ Good: `@workspace Generate test harness for UserService with unit and integration tests using the test-harness pattern`

### 4. Combine Patterns
```
@workspace Implement user authentication using feature-development workflow, then run security-scan
```

## Prompt Templates

### For Workflows
```
@workspace [Action] [feature/component] using the [workflow-name] workflow

Examples:
- Implement payment processing using the feature-development workflow
- Debug memory leak using the smart-fix workflow
- Review authentication code using the full-review workflow
```

### For Tools
```
@workspace [Action] [target] following the [tool-name] pattern

Examples:
- Generate API scaffolding for orders following the api-scaffold pattern
- Run security scan on Docker configuration
- Create database migration for user tables following db-migrate pattern
```

## Troubleshooting

### Copilot Doesn't Find Instructions

**Issue**: GitHub Copilot doesn't seem to use the custom instructions

**Solutions**:
1. Ensure files are in `.github/copilot/` directory
2. Verify `.github/copilot-instructions.md` exists
3. Restart VS Code / your IDE
4. Use `@workspace` prefix in chat
5. Try being more explicit: "Use the api-scaffold pattern from .github/copilot/tools/"

### Pattern Not Working as Expected

**Issue**: Copilot gives generic response instead of following pattern

**Solutions**:
1. Be more specific in your prompt
2. Reference the pattern file directly: "Following .github/copilot/tools/api-scaffold.md, generate..."
3. Include more context about what you want
4. Break complex requests into steps

### Want Old Claude Code Behavior

**Issue**: Need exact Claude Code behavior

**Solution**: Original files are still in `workflows/` and `tools/` directories. You can:
1. Continue using Claude Code with original files
2. Reference original files for detailed specifications
3. Use both systems (Claude Code + Copilot) for different tasks

## Feature Comparison

| Feature | Claude Code | GitHub Copilot |
|---------|-------------|----------------|
| **Invocation** | Slash commands | Natural language |
| **Location** | Global `~/.claude/` | Per-project `.github/` |
| **Syntax** | Fixed `/workflows:name` | Flexible prompts |
| **Context** | Command arguments | Full workspace context |
| **Integration** | CLI-based | IDE-integrated |
| **Learning Curve** | Memorize commands | Natural conversation |
| **Flexibility** | Structured | Freeform |
| **Discoverability** | Command list | Ask in chat |

## Benefits of GitHub Copilot Version

### Advantages
- ✅ No special syntax to remember
- ✅ Natural language interaction
- ✅ Full workspace context awareness
- ✅ IDE integration (VS Code, JetBrains, etc.)
- ✅ Can combine multiple patterns in one request
- ✅ Easier for team collaboration
- ✅ Works where you code

### When to Use Claude Code
- Need CLI-based workflow
- Prefer structured commands
- Working outside IDE
- Command-line automation scripts

### When to Use GitHub Copilot
- Working in IDE (VS Code, JetBrains)
- Prefer natural language
- Want workspace context
- Team collaboration in IDE

## Advanced Usage

### Combining Patterns
```
@workspace Using feature-development workflow:
1. Generate API scaffolding for user service
2. Add comprehensive tests with test-harness
3. Run security-scan
4. Generate k8s-manifest for deployment
```

### Context-Aware Development
```
@workspace Looking at UserService.ts, generate integration tests following test-harness pattern
```

### Iterative Refinement
```
@workspace Generate API endpoint for users
// Review output
@workspace Add input validation following api-scaffold best practices
// Review again
@workspace Add rate limiting from api-scaffold pattern
```

## FAQ

**Q: Can I use both Claude Code and GitHub Copilot?**
A: Yes! Original files remain in `workflows/` and `tools/`. Use Claude Code with those, GitHub Copilot with `.github/copilot/`.

**Q: Do I need to install anything special?**
A: Just GitHub Copilot extension in your IDE. Clone this repo to your workspace.

**Q: Will patterns work outside this repo?**
A: Yes, copy `.github/copilot*` to any project to use patterns there.

**Q: Can I customize patterns?**
A: Yes! Edit any `.md` file in `.github/copilot/` to customize for your needs.

**Q: How do I discover available patterns?**
A: Ask Copilot: `@workspace What workflows are available?` or browse `.github/copilot/`

**Q: Can I create my own patterns?**
A: Yes! Add new `.md` files to `.github/copilot/workflows/` or `.github/copilot/tools/`

## Getting Help

- **Documentation**: See `.github/CONVERSION_SUMMARY.md`
- **Examples**: Check `examples/` directory  
- **Issues**: [GitHub Issues](https://github.com/T-450/commands/issues)
- **Original Docs**: Original Claude Code files still in `workflows/` and `tools/`

## Next Steps

1. ✅ Clone repository or copy `.github/copilot*` to your project
2. ✅ Open GitHub Copilot Chat in your IDE
3. ✅ Try: `@workspace What workflows are available?`
4. ✅ Start using: `@workspace Generate API using api-scaffold pattern`
5. ✅ Explore all 57 patterns in `.github/copilot/`

Welcome to AI-assisted development with GitHub Copilot! 🚀
