# GitHub Copilot Implementation Plan
## Based on Official VSCode Documentation

### Current Issues with Existing Conversion

After reviewing the official GitHub Copilot documentation for VSCode, the current implementation has several issues:

1. **Incorrect Structure**: Files are placed in `.github/copilot/` but should be in `.github/instructions/`
2. **Missing Path-Specific Instructions**: Not using `.instructions.md` naming convention with glob patterns
3. **Incorrect Discovery**: Copilot won't automatically discover files in `.github/copilot/` subdirectories
4. **No Frontmatter**: Path-specific files need YAML frontmatter with `applyTo` patterns

### Official GitHub Copilot Custom Instructions Structure

According to official documentation, GitHub Copilot for VSCode supports **three types** of custom instructions:

#### 1. Repository-Wide Instructions
- **File**: `.github/copilot-instructions.md` ✅ (We have this)
- **Scope**: Applies to ALL requests in the repository
- **Discovery**: Automatic
- **Purpose**: Global development guidance, standards, conventions

#### 2. Path-Specific Instructions
- **Location**: `.github/instructions/` directory
- **Naming**: `NAME.instructions.md` (must end with `.instructions.md`)
- **Frontmatter Required**: YAML block with `applyTo` glob pattern
- **Scope**: Applies only to files matching the glob pattern
- **Example**:
  ```markdown
  ---
  applyTo: "**/*.ts,**/*.tsx"
  ---
  # TypeScript Guidelines
  - Use strict mode
  - Prefer interfaces over types
  ```

#### 3. Agent Instructions (Experimental)
- **File**: `AGENTS.md` (or `CLAUDE.md`, `GEMINI.md`)
- **Location**: Anywhere in repository (nearest takes precedence)
- **Purpose**: AI agent coordination
- **Status**: Experimental, disabled by default

### What GitHub Copilot Actually Reads

**In VSCode, Copilot Chat reads:**
- ✅ `.github/copilot-instructions.md` (repository-wide)
- ✅ `.github/instructions/*.instructions.md` (path-specific, when matching)
- ❌ `.github/copilot/workflows/*.md` (NOT automatically discovered)
- ❌ `.github/copilot/tools/*.md` (NOT automatically discovered)

**Key Insight**: Copilot does NOT recursively scan subdirectories under `.github/` for custom instructions!

### Recommended Restructure

Based on official documentation, here's the proper structure:

```
.github/
├── copilot-instructions.md              # ✅ Repository-wide (keep as-is, enhance)
├── instructions/                        # 📁 NEW - Path-specific instructions
│   ├── workflows.instructions.md        # Pattern-based workflow guidance
│   ├── tools.instructions.md            # Pattern-based tool guidance
│   ├── python.instructions.md           # Language-specific
│   ├── typescript.instructions.md       # Language-specific
│   ├── api.instructions.md              # Domain-specific
│   ├── testing.instructions.md          # Testing-specific
│   ├── security.instructions.md         # Security-specific
│   └── devops.instructions.md           # DevOps-specific
├── MIGRATION_GUIDE.md                   # Keep
├── CONVERSION_SUMMARY.md                # Keep
└── copilot/                             # Keep for reference
    ├── workflows/                       # Keep as documentation
    └── tools/                           # Keep as documentation
```

### Implementation Strategy

#### Phase 1: Enhance Repository-Wide Instructions
**File**: `.github/copilot-instructions.md`

**Current**: Basic overview with general patterns
**Improve to**:
- Clear introduction to the 57 patterns
- How to reference specific patterns by name
- Examples of how to invoke patterns in Copilot Chat
- Index of all available patterns with brief descriptions
- Best practices for combining patterns

#### Phase 2: Create Path-Specific Instructions
**Directory**: `.github/instructions/`

Create focused instruction files that apply to specific file types:

1. **workflows.instructions.md**
   - `applyTo: "workflows/**/*.md"`
   - Guidance on using workflow patterns
   - How to reference and invoke workflows

2. **tools.instructions.md**
   - `applyTo: "tools/**/*.md"`
   - Guidance on using tool patterns
   - How to reference and invoke tools

3. **Language-Specific**:
   - `python.instructions.md` → `applyTo: "**/*.py"`
   - `typescript.instructions.md` → `applyTo: "**/*.ts,**/*.tsx"`
   - `go.instructions.md` → `applyTo: "**/*.go"`
   - Reference relevant patterns for each language

4. **Domain-Specific**:
   - `api.instructions.md` → `applyTo: "**/api/**/*,**/routes/**/*"`
   - `testing.instructions.md` → `applyTo: "**/*test*.{py,ts,js,go},**/tests/**/*"`
   - `security.instructions.md` → `applyTo: "**/security/**/*,**/auth/**/*"`
   - `devops.instructions.md` → `applyTo: "**/*.{yml,yaml,Dockerfile,tf}"`

#### Phase 3: Update Documentation
1. **README.md**: Update with accurate VSCode Copilot usage
2. **MIGRATION_GUIDE.md**: Add section on path-specific instructions
3. Keep `.github/copilot/` as reference documentation

### How Users Will Use This

#### Repository-Wide Guidance
When working anywhere in the repo:
```
@workspace Implement OAuth2 authentication
→ Copilot reads .github/copilot-instructions.md
→ Sees index of 57 patterns
→ Can reference feature-development.md workflow
```

#### Path-Specific Guidance
When editing a Python file:
```
# User editing src/api/users.py
@workspace Add user authentication endpoint
→ Copilot reads .github/copilot-instructions.md (global)
→ ALSO reads .github/instructions/python.instructions.md (matches *.py)
→ ALSO reads .github/instructions/api.instructions.md (matches api/**)
→ Gets Python-specific + API-specific + global guidance
```

#### Referencing Detailed Patterns
```
@workspace Following .github/copilot/tools/api-scaffold.md, generate user endpoints
→ User explicitly references the detailed documentation
→ Copilot can read it as context
```

### Key Differences from Current Implementation

| Aspect | Current | Correct |
|--------|---------|---------|
| **Structure** | `.github/copilot/workflows/` | `.github/instructions/` |
| **Naming** | `workflow-name.md` | `purpose.instructions.md` |
| **Frontmatter** | None | Required YAML with `applyTo` |
| **Scope** | Not automatically applied | Automatically applied to matching files |
| **Discovery** | Manual reference only | Automatic when editing matching files |
| **Purpose** | Documentation | Active instructions |

### What to Keep vs. Change

**Keep (Reference Documentation)**:
- `.github/copilot/workflows/*.md` - Detailed workflow specs
- `.github/copilot/tools/*.md` - Detailed tool specs
- These serve as comprehensive reference docs that users can explicitly reference

**Change/Add**:
- Enhance `.github/copilot-instructions.md` with better indexing
- Create `.github/instructions/` with path-specific instruction files
- Each `.instructions.md` file references the detailed specs in `copilot/`

### Benefits of This Approach

1. **Automatic Context**: Copilot automatically includes relevant instructions based on file type
2. **Layered Guidance**: Global + path-specific + explicit reference
3. **Standards Compliant**: Follows official GitHub Copilot documentation
4. **Better UX**: Users get relevant guidance without manual reference
5. **Scalable**: Easy to add new path-specific instructions

### Example: Complete Flow

User wants to create a FastAPI endpoint:

1. **Creates** `src/api/users.py`
2. **Copilot automatically loads**:
   - `.github/copilot-instructions.md` (sees api-scaffold pattern exists)
   - `.github/instructions/python.instructions.md` (Python best practices)
   - `.github/instructions/api.instructions.md` (API patterns, references api-scaffold.md)
3. **User asks**: `@workspace Create CRUD endpoints for users`
4. **Copilot responds with**:
   - FastAPI code (from Python instructions)
   - Following api-scaffold pattern (from API instructions)
   - Security best practices (from global instructions)
   - Can reference detailed `.github/copilot/tools/api-scaffold.md` for specifics

### Implementation Checklist

- [ ] Enhance `.github/copilot-instructions.md` with pattern index
- [ ] Create `.github/instructions/` directory
- [ ] Create path-specific instruction files:
  - [ ] `workflows.instructions.md`
  - [ ] `tools.instructions.md`
  - [ ] `python.instructions.md`
  - [ ] `typescript.instructions.md`
  - [ ] `javascript.instructions.md`
  - [ ] `go.instructions.md`
  - [ ] `api.instructions.md`
  - [ ] `testing.instructions.md`
  - [ ] `security.instructions.md`
  - [ ] `devops.instructions.md`
- [ ] Update README.md with accurate usage
- [ ] Update MIGRATION_GUIDE.md with path-specific info
- [ ] Keep `.github/copilot/` as reference documentation
- [ ] Test with actual VSCode GitHub Copilot

### References

- [VS Code Custom Instructions Docs](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)
- [GitHub Custom Instructions Docs](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions?tool=vscode)
- [openai/agents.md](https://github.com/openai/agents.md) (for agent instructions)
