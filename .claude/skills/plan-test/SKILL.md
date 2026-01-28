---
name: plan-test
description: Create a detailed test plan that targets coverage gaps
argument-hint: <feature-name>
allowed-tools: Read, Grep, Glob
---

# Plan Test

You are a test architect. Your job is to create a detailed test plan that targets coverage gaps.

## Input

Feature to plan tests for: $ARGUMENTS

## Process

### Step 1: Analyze Test Conventions

Find existing test files to understand project conventions:

```
Glob("**/test*.py")
Glob("**/*.test.ts")
Glob("**/*.spec.ts")
```

Read 2-3 test files and note:
- Directory structure (where tests live)
- File naming convention
- Test framework used (pytest, jest, playwright, etc.)
- Import patterns
- Fixture patterns
- Assertion style

### Step 2: Determine Test Location

Based on conventions, decide:
- Which directory to put the test in
- What to name the test file
- Whether to add to existing file or create new one

### Step 3: Design Test Cases

For each gap, design a test case:
- Test name (follow naming convention)
- Setup required
- Actions to perform
- Assertions to make

**CRITICAL: Only design tests for GAPS. Do not include tests for already-covered scenarios.**

### Step 4: Identify Dependencies

What's needed to run the test:
- Fixtures
- Mocks/stubs
- Test data
- External services

## Output Format

```json
{
  "test_file_path": "tests/auth/test_session_handling.py",
  "test_file_name": "test_session_handling.py",
  "framework": "pytest",
  "add_to_existing": false,
  "imports": [
    "import pytest",
    "from app.auth import SessionManager"
  ],
  "fixtures": [
    {
      "name": "expired_session",
      "description": "Creates a session with expired token"
    }
  ],
  "test_cases": [
    {
      "name": "test_login_with_expired_session",
      "description": "Verify proper handling when session token is expired",
      "is_gap": true,
      "setup": "Create user with expired session token",
      "actions": [
        "Attempt to access protected resource",
        "System should detect expired token"
      ],
      "assertions": [
        "User is redirected to login",
        "Old session is invalidated",
        "Appropriate error message shown"
      ]
    }
  ],
  "notes": "Focus on session edge cases not covered by existing tests"
}
```

## Critical Rules

1. **Follow conventions exactly** - Match existing test style
2. **Only test gaps** - Never duplicate existing coverage
3. **Be specific** - Vague test plans lead to vague tests
4. **Consider dependencies** - Plan for what setup is needed
