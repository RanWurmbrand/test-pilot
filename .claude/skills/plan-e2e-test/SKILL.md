---
name: plan-e2e-test
description: Create a detailed e2e test plan that targets coverage gaps
argument-hint: <feature-name>
allowed-tools: Read, Grep, Glob
---

# Plan E2E Test

You are a test architect. Your job is to create a detailed test plan that targets coverage gaps.

This skill focuses on **e2e (end-to-end) test planning** - UI workflows, page interactions, user journeys.

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

**E2E specific - find e2e test conventions:**
```
Glob("**/e2e/**/*.test.ts")
Glob("**/e2e/**/*.spec.ts")
```

Read 2-3 test files and note:
- Directory structure (where tests live)
- File naming convention
- Test framework used (pytest, jest, playwright, etc.)
- Import patterns
- Fixture patterns
- Assertion style

**E2E specific - also note:**
- Page object patterns (how they're imported and used)
- How the app/browser is launched
- How tests wait for elements
- Selector patterns used (data-testid, css, xpath)
- How user flows are structured in tests

### Step 2: Determine Test Location

Based on conventions, decide:
- Which directory to put the test in
- What to name the test file
- Whether to add to existing file or create new one

**E2E specific considerations:**
- Should it go in the e2e folder?
- Does it belong with related user flow tests?
- Should it be a new file or added to existing flow test?

### Step 3: Design Test Cases

For each gap, design a test case:
- Test name (follow naming convention)
- Setup required
- Actions to perform
- Assertions to make

**CRITICAL: Only design tests for GAPS. Do not include tests for already-covered scenarios.**

**E2E specific - for each test case also define:**
- Page objects needed
- User flow steps (navigate, click, fill, wait)
- Selectors to use (follow existing patterns)
- What to wait for before assertions
- Expected UI state after actions

### Step 4: Identify Dependencies

What's needed to run the test:
- Fixtures
- Mocks/stubs
- Test data
- External services

**E2E specific dependencies:**
- Page objects (do they exist or need creation?)
- Test user accounts
- App state setup (login, data seeding)
- Browser/viewport configuration

## Output Format

```json
{
  "test_file_path": "tests/e2e/auth/session-handling.test.ts",
  "test_file_name": "session-handling.test.ts",
  "framework": "playwright",
  "add_to_existing": false,
  "imports": [
    "import { test, expect } from '@playwright/test'",
    "import { LoginPage } from '../page-objects/LoginPage'",
    "import { DashboardPage } from '../page-objects/DashboardPage'"
  ],
  "page_objects_needed": [
    {
      "name": "LoginPage",
      "exists": true,
      "file": "tests/e2e/page-objects/LoginPage.ts"
    },
    {
      "name": "SessionExpiredModal",
      "exists": false,
      "needs_creation": true
    }
  ],
  "fixtures": [
    {
      "name": "expiredSessionUser",
      "description": "User with expired session token"
    }
  ],
  "test_cases": [
    {
      "name": "should redirect to login when session expires",
      "description": "Verify user is redirected when session token is expired",
      "is_gap": true,
      "setup": "Login as user, then expire session token",
      "user_flow_steps": [
        "Navigate to dashboard",
        "Wait for session expired modal",
        "Click 'Login Again' button",
        "Verify redirected to login page"
      ],
      "selectors": [
        "[data-testid='session-expired-modal']",
        "[data-testid='login-again-button']"
      ],
      "wait_for": "session-expired-modal to be visible",
      "assertions": [
        "Session expired modal is shown",
        "User is redirected to login page",
        "Previous session is invalidated"
      ]
    }
  ],
  "notes": "Focus on session edge cases. Check if SessionExpiredModal page object needs to be created."
}
```

## Critical Rules

1. **Follow conventions exactly** - Match existing test style
2. **Only test gaps** - Never duplicate existing coverage
3. **Be specific** - Vague test plans lead to vague tests
4. **Consider dependencies** - Plan for what setup is needed
5. **E2E specific: Use real page objects** - Check they exist, note if they need creation
6. **E2E specific: Define complete user flows** - Each step should be clear
