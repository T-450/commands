# Improve Agent Workflow - GitHub Copilot Instructions

## Overview

This workflow helps you improve and refine GitHub Copilot's instructions based on real-world usage patterns, feedback, and observed shortcomings. It analyzes performance, identifies patterns in failures or suboptimal outputs, and updates instructions with improvements.

## When to Use

**Prompt GitHub Copilot with:**
- "Analyze and improve the api-scaffold instructions based on recent usage"
- "Review the test-automator pattern and suggest improvements"
- "Update the deployment-engineer instructions based on user feedback"
- "Refine the security-auditor pattern to address common gaps"

## Improvement Process

### Step 1: Analyze Recent Usage

**Prompt GitHub Copilot:**
"Analyze recent uses of [instruction pattern name]. Review the last 10-20 interactions and identify patterns in performance, user corrections, and outcomes."

Analysis areas:
- **Success rate**: How often does the pattern achieve desired results?
- **Common failures**: What types of tasks consistently fail?
- **User corrections**: What do users frequently fix or modify?
- **Completeness**: Are outputs comprehensive or missing key elements?
- **Accuracy**: Are suggestions correct and appropriate?
- **Relevance**: Do outputs match user intent?

### Step 2: Identify Improvement Patterns

**Prompt GitHub Copilot:**
"Identify patterns in failures, user corrections, and suboptimal outputs for [instruction pattern]. Categorize issues by type."

Look for:

**Failed Tasks**
- Tasks that error out or produce incorrect results
- Misunderstood requirements
- Missing critical functionality
- Incomplete implementations
- Wrong technology choices

**User Corrections**
- Code that users consistently modify
- Missing imports or dependencies
- Incorrect configurations
- Suboptimal patterns being replaced
- Security issues being fixed

**Suboptimal Outputs**
- Code that works but isn't best practice
- Verbose or inefficient implementations
- Missing error handling
- Inadequate documentation
- Poor test coverage
- Performance issues

### Step 3: Update Instructions

**Prompt GitHub Copilot:**
"Update the [instruction pattern] with improvements addressing identified issues. Add new examples, clarify instructions, and include additional constraints."

Update elements:

**Add New Examples**
- Examples covering previously failed scenarios
- Edge cases that were missed
- Common use cases that weren't documented
- Success patterns that should be replicated

**Clarify Instructions**
- More specific guidance for ambiguous areas
- Clearer requirements and constraints
- Explicit dos and don'ts
- Step-by-step procedures for complex tasks

**Additional Constraints**
- Security requirements that were overlooked
- Performance benchmarks to meet
- Required validations and checks
- Mandatory documentation elements
- Code quality standards

**Improved Prompts**
- Better example prompts showing proper usage
- Anti-patterns to avoid
- Context requirements
- Expected output formats

### Step 4: Test on Recent Scenarios

**Prompt GitHub Copilot:**
"Test the improved [instruction pattern] on these recent scenarios that previously failed or produced suboptimal results: [list scenarios]"

Testing approach:
- **Regression testing**: Ensure previous successes still work
- **Failure retesting**: Verify identified failures now succeed
- **Edge case validation**: Test newly added examples
- **User correction scenarios**: Verify corrections no longer needed
- **Performance validation**: Check improvements in quality and speed

### Step 5: Document and Save Improvements

**Prompt GitHub Copilot:**
"Document the improvements made to [instruction pattern] including what changed, why, and expected impact."

Documentation includes:

**Change Log**
```markdown
## Version History

### Version 2.1 (2024-01-15)
**Changed:**
- Added explicit error handling examples
- Clarified database connection pooling configuration
- Added TypeScript type definitions to all examples

**Reason:**
- 60% of users were adding error handling after initial generation
- Connection pool issues appeared in 40% of deployments
- Type safety was missing from generated code

**Expected Impact:**
- Reduce post-generation modifications by 50%
- Eliminate common deployment issues
- Improve type safety and IDE support
```

**Before/After Examples**
```markdown
## Improvement Example: Error Handling

### Before
```javascript
async function fetchUser(id) {
  const user = await db.query('SELECT * FROM users WHERE id = ?', [id]);
  return user;
}
```

### After
```javascript
async function fetchUser(id: string): Promise<User> {
  try {
    const user = await db.query<User>(
      'SELECT * FROM users WHERE id = ?',
      [id]
    );
    
    if (!user) {
      throw new NotFoundError(`User ${id} not found`);
    }
    
    return user;
  } catch (error) {
    logger.error('Failed to fetch user', { id, error });
    throw new DatabaseError('User fetch failed', { cause: error });
  }
}
```
```

## Common Improvement Categories

### Completeness Improvements
- Add missing error handling
- Include necessary imports
- Add configuration files
- Include deployment instructions
- Add monitoring/logging
- Include security measures

### Accuracy Improvements
- Fix incorrect API usage
- Update deprecated patterns
- Correct security vulnerabilities
- Fix performance anti-patterns
- Update outdated best practices

### Clarity Improvements
- Simplify complex explanations
- Add more examples
- Break down large steps
- Add visual diagrams
- Include troubleshooting guides
- Clarify prerequisites

### Quality Improvements
- Enforce coding standards
- Add type safety
- Improve test coverage
- Enhance documentation
- Add validation checks
- Include performance optimizations

## Metrics to Track

Track these metrics before and after improvements:

- **Success rate**: % of tasks completed successfully
- **Modification rate**: % of outputs requiring user changes
- **Time to completion**: Average time to complete tasks
- **User satisfaction**: Qualitative feedback scores
- **Reuse rate**: How often the pattern is reused
- **Error rate**: % of tasks resulting in errors

## Example Improvement Workflow

**Complete improvement prompt:**
"Improve the 'api-scaffold' instruction pattern:
1. Analyze the last 20 uses and identify common issues
2. Key observations: users add authentication 80% of the time, error handling is often incomplete, OpenAPI specs are missing
3. Update the pattern to include authentication by default, comprehensive error handling examples, and OpenAPI documentation generation
4. Add examples for REST, GraphQL, and WebSocket APIs
5. Test on 3 recent scenarios that needed significant user corrections
6. Document changes and expected impact"

## Best Practices

1. **Data-driven**: Base improvements on actual usage data, not assumptions
2. **Incremental**: Make small, testable improvements rather than large rewrites
3. **Backward compatible**: Ensure existing uses still work
4. **Well-documented**: Clearly explain what changed and why
5. **Validated**: Test improvements before deploying
6. **User-focused**: Address actual user pain points
7. **Version controlled**: Track changes over time

## Related Patterns

- `full-review.md` - Review instruction quality
- `test-harness.md` (tool) - Test instruction improvements
- `api-documenter.md` (tool) - Document instruction changes
- All instruction patterns can be improved using this workflow
