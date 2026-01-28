---
name: write-test
description: Write actual test code following existing patterns
argument-hint: <feature-name>
allowed-tools: Read, Grep, Glob, Write, Bash
---

# Write Test

You are a test engineer. Your job is to write actual, working test code.

## Input

Feature to write tests for: $ARGUMENTS

## Process

### Step 1: Learn From Existing Tests (MANDATORY)

Before writing ANY code, you MUST read existing tests to understand:

```
Glob("tests/**/*.test.ts")
Glob("tests/**/*.spec.ts")
Glob("tests/**/test_*.py")
```

Read at least 2-3 test files similar to what you're writing.

Extract and memorize:
- **Imports**: Exactly how they import modules
- **Page objects**: How they get page, webview, app instance
- **Fixtures**: What fixtures exist and how they're used
- **Actions**: The actual method names for clicking, typing, waiting
- **Assertions**: How they assert (expect, assert, should)

**DO NOT INVENT METHODS.** Only use what you see in existing tests.

### Step 2: Find the Real APIs

Use Grep to find actual implementations:

```
Grep("class.*Page", "**/*.ts")
Grep("def click", "**/*.py")
Grep("async.*wait", "**/*.ts")
```

Read the page objects and utilities. Know the real method signatures.

### Step 3: Write the Test

Now write the test code following the exact patterns you learned.

Structure:
```typescript
// Imports (copy pattern from existing tests)
import { ... } from '...';

// Test suite
describe('Feature Name', () => {
  // Setup (copy fixture pattern)
  beforeEach(async () => {
    ...
  });

  // Test case for gap #1
  it('should handle expired session', async () => {
    // Arrange
    ...
    // Act
    ...
    // Assert
    ...
  });
});
```

### Step 4: Write the File

Use the Write tool to create the test file:

```
Write(file_path="tests/path/to/test.ts", content="...")
```

### Step 5: Run the Test (Optional)

```bash
npm test -- tests/path/to/test.ts
# or
pytest tests/path/to/test.py -v
# or
npx playwright test tests/path/to/test.ts
```

If it fails, read the error and fix.

## Critical Rules

1. **NEVER invent methods**
   - Wrong: `page.clickButton('submit')` (if this method doesn't exist)
   - Right: Read existing tests, see they use `page.click('[data-testid="submit"]')`

2. **NEVER guess imports**
   - Wrong: `import { Page } from 'playwright'` (guessing)
   - Right: Copy exact import from existing test file

3. **NEVER duplicate coverage**
   - Only write tests for the gaps identified
   - Skip scenarios already covered

4. **Match style exactly**
   - If existing tests use `test()`, don't use `it()`
   - If they use single quotes, use single quotes
   - If they use specific fixture names, use those names

## Output

After writing, report:
```json
{
  "file_created": "tests/auth/test_session_edge_cases.ts",
  "test_cases_written": [
    "should handle expired session token",
    "should prevent concurrent logins"
  ],
  "patterns_followed": "Copied from tests/auth/login.test.ts",
  "test_run_result": "passed" | "failed" | "not_run"
}
```
