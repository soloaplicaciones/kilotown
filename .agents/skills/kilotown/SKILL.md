```markdown
# kilotown Development Patterns

> Auto-generated skill from repository analysis

## Overview
This skill provides guidance on contributing to the `kilotown` TypeScript codebase. It covers code style conventions, documentation workflows, and testing patterns. The repository emphasizes clear documentation, consistent code organization, and collaborative authoring of guides for the Hermes Agent.

## Coding Conventions

### File Naming
- Use **camelCase** for file names.
  - Example: `hermesAgentConfig.ts`, `userGuideSection.ts`

### Imports
- Use **relative imports** for modules within the project.
  - Example:
    ```typescript
    import { HermesAgent } from './hermesAgent';
    ```

### Exports
- Use **named exports** for functions, classes, or constants.
  - Example:
    ```typescript
    export function startAgent() { /* ... */ }
    export const AGENT_VERSION = '1.0.0';
    ```

### Commit Messages
- Freeform style, sometimes prefixed with `docs`.
- Keep messages concise and descriptive (average ~61 characters).
  - Example: `docs: add configuration section to user guide`

## Workflows

### Add New Documentation Guide
**Trigger:** When you want to add a new guide or manual section to the Hermes Agent documentation (for users or developers).  
**Command:** `/new-doc-guide`

1. Create a new markdown file in the appropriate directory:
    - For user guides: `docs/hermes-agent/guia-usuario/`
    - For developer guides: `docs/hermes-agent/guia-desarrollador/`
2. Write the content for the guide (e.g., configuration, architecture, extension).
3. Commit the new file with a descriptive message.

**Example:**
```bash
# Create a new user guide
touch docs/hermes-agent/guia-usuario/configuracion.md

# Edit the file with your guide content

# Commit your changes
git add docs/hermes-agent/guia-usuario/configuracion.md
git commit -m "docs: add configuration guide for Hermes Agent"
git push
```

## Testing Patterns

- **Test files** use the pattern `*.test.*` (e.g., `hermesAgent.test.ts`).
- The specific testing framework is unknown, but standard TypeScript test structures likely apply.
- Place test files alongside the code they test or in a dedicated test directory.

**Example:**
```typescript
// hermesAgent.test.ts
import { startAgent } from './hermesAgent';

describe('startAgent', () => {
  it('should initialize the agent successfully', () => {
    expect(startAgent()).toBeTruthy();
  });
});
```

## Commands

| Command         | Purpose                                                      |
|-----------------|--------------------------------------------------------------|
| /new-doc-guide  | Start the process to add a new documentation guide           |

```