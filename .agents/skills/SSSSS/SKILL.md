```markdown
# SSSSS Development Patterns

> Auto-generated skill from repository analysis

## Overview
This skill teaches the core development patterns and conventions used in the SSSSS TypeScript codebase. It covers file organization, code style, commit message standards, and testing patterns, enabling consistent contributions and streamlined collaboration.

## Coding Conventions

### File Naming
- Use **snake_case** for all file names.
  - Example:  
    ```
    user_service.ts
    data_processor.test.ts
    ```

### Import Style
- Use **relative imports** for referencing modules.
  - Example:
    ```typescript
    import { processData } from './data_processor';
    ```

### Export Style
- Use **named exports** for all exported functions, classes, or constants.
  - Example:
    ```typescript
    // In user_service.ts
    export function createUser() { ... }
    export const USER_ROLE = 'admin';
    ```

### Commit Messages
- Follow **conventional commit** style.
- Use the `docs` prefix for documentation changes.
- Keep commit messages concise (~58 characters on average).
  - Example:
    ```
    docs: update README with setup instructions
    ```

## Workflows

_No automated workflows were detected in this repository._

## Testing Patterns

- Test files use the `*.test.*` naming pattern.
  - Example:  
    ```
    math_utils.test.ts
    ```
- Testing framework is **unknown**; check existing test files for setup.
- Place test files alongside or near the code they test.

#### Example Test File
```typescript
// math_utils.test.ts
import { add } from './math_utils';

describe('add', () => {
  it('adds two numbers', () => {
    expect(add(2, 3)).toBe(5);
  });
});
```

## Commands
| Command | Purpose |
|---------|---------|
| /test   | Run all test files matching `*.test.*` |
| /lint   | Check code for style and formatting issues |
| /docs   | Generate or update documentation |
```