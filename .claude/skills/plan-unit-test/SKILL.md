---
name: plan-unit-test
description: Create a detailed unit test plan that targets coverage gaps
argument-hint: <feature-name>
allowed-tools: Read, Grep, Glob
---

# Plan Unit Test

You are a test architect. Your job is to create a detailed test plan that targets coverage gaps.

This skill focuses on **unit test planning** - individual functions, methods, classes in isolation.

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

**Unit specific - find unit test conventions:**
```
Glob("**/unit/**/*.test.ts")
Glob("**/unit/**/*.spec.ts")
Glob("**/test_*.py")
Glob("**/*_test.py")
```

Read 2-3 test files and note:
- Directory structure (where tests live)
- File naming convention
- Test framework used (pytest, jest, playwright, etc.)
- Import patterns
- Fixture patterns
- Assertion style

**Unit specific - also note:**
- Mock patterns (how dependencies are mocked)
- Fixture setup patterns
- How isolated tests are structured
- Parameterized test patterns (if used)
- How edge cases are organized

### Step 2: Determine Test Location

Based on conventions, decide:
- Which directory to put the test in
- What to name the test file
- Whether to add to existing file or create new one

**Unit specific considerations:**
- Should it go in the unit folder?
- Does it belong with related function tests?
- Should it mirror the source file structure?

### Step 3: Design Test Cases

For each gap, design a test case:
- Test name (follow naming convention)
- Setup required
- Actions to perform
- Assertions to make

**CRITICAL: Only design tests for GAPS. Do not include tests for already-covered scenarios.**

**Unit specific - for each test case also define:**
- Function/method under test
- Input values (including edge cases)
- Expected output/return value
- Expected exceptions (if any)
- Mocks needed for dependencies

### Step 4: Identify Dependencies

What's needed to run the test:
- Fixtures
- Mocks/stubs
- Test data
- External services

**Unit specific dependencies:**
- Mocks for external dependencies (database, API, etc.)
- Fixtures for test data
- Parameterized inputs for variations
- Setup/teardown for state

## Output Format

```json
{
  "test_file_path": "tests/unit/test_validators.py",
  "test_file_name": "test_validators.py",
  "framework": "pytest",
  "add_to_existing": true,
  "imports": [
    "import pytest",
    "from unittest.mock import Mock, patch",
    "from src.validators import validate_email, validate_phone"
  ],
  "mocks_needed": [
    {
      "name": "mock_database",
      "what_it_mocks": "database connection",
      "why_needed": "isolate validator from database calls"
    }
  ],
  "fixtures": [
    {
      "name": "valid_emails",
      "description": "List of valid email formats for parameterized testing"
    },
    {
      "name": "invalid_emails",
      "description": "List of invalid email formats"
    }
  ],
  "test_cases": [
    {
      "name": "test_validate_email_with_unicode",
      "description": "Verify email validation handles unicode characters",
      "is_gap": true,
      "function_under_test": "validate_email",
      "setup": "No special setup needed",
      "inputs": [
        "user@例え.jp",
        "用户@example.com"
      ],
      "expected_outputs": [
        "True (valid unicode domain)",
        "True (valid unicode local part)"
      ],
      "mocks_used": [],
      "assertions": [
        "Returns True for valid unicode emails",
        "Does not raise exception"
      ]
    },
    {
      "name": "test_validate_phone_with_country_code",
      "description": "Verify phone validation handles international country codes",
      "is_gap": true,
      "function_under_test": "validate_phone",
      "setup": "Mock country code lookup service",
      "inputs": [
        "+1-555-123-4567",
        "+44-20-7946-0958",
        "+81-3-1234-5678"
      ],
      "expected_outputs": [
        "True for all valid international formats"
      ],
      "mocks_used": ["mock_country_code_service"],
      "assertions": [
        "Returns True for valid international formats",
        "Correctly parses country code prefix"
      ]
    }
  ],
  "notes": "Add parameterized tests for multiple input variations. Consider adding property-based testing for comprehensive coverage."
}
```

## Critical Rules

1. **Follow conventions exactly** - Match existing test style
2. **Only test gaps** - Never duplicate existing coverage
3. **Be specific** - Vague test plans lead to vague tests
4. **Consider dependencies** - Plan for what setup is needed
5. **Unit specific: Test in isolation** - Mock external dependencies
6. **Unit specific: Cover edge cases** - Plan for boundary values, null, empty inputs
