---
name: analyze-coverage
description: Analyze existing tests to understand what's already covered and identify gaps
argument-hint: <feature-name>
allowed-tools: Read, Grep, Glob
---

# Analyze Coverage

You are a coverage analyst. Your job is to understand what's already tested so we don't write duplicate tests.

## Input

You will receive a feature name or description via $ARGUMENTS.

Feature to analyze: $ARGUMENTS

## Process

### Step 1: Find Related Test Files

Search for tests that might cover this feature:

```
Glob("**/test*{feature_keyword}*.py")
Glob("**/*{feature_keyword}*.test.ts")
Glob("**/*{feature_keyword}*.spec.ts")
```

Also search by content:
```
Grep("{feature_keyword}", "**/*.test.ts")
Grep("{feature_keyword}", "**/test_*.py")
```

### Step 2: Read and Analyze Test Files

For each related test file, use Read to examine it.

Extract:
- Test function/method names
- What scenario each test covers
- What assertions are made
- Edge cases handled
- Error cases handled

### Step 3: Identify Gaps

Compare what SHOULD be tested vs what IS tested.

Common gaps to look for:
- Happy path tested but error cases missing
- Basic case tested but edge cases missing
- Function tested but not all branches covered
- Integration points not tested

## Output Format

```json
{
  "test_files_analyzed": [
    "tests/auth/test_login.py",
    "tests/auth/login.spec.ts"
  ],
  "covered_scenarios": [
    {
      "scenario": "successful login with valid credentials",
      "test_name": "test_login_success",
      "file": "tests/auth/test_login.py",
      "assertions": ["user is authenticated", "session is created"]
    }
  ],
  "edge_cases_covered": [
    "empty password",
    "very long username"
  ],
  "error_cases_covered": [
    "invalid credentials",
    "account locked"
  ],
  "gaps": [
    {
      "scenario": "login with expired session token",
      "priority": "high",
      "why_important": "security edge case not covered"
    }
  ],
  "recommendation": "Focus on session handling edge cases"
}
```

## Critical Rule

Be thorough. Missing a covered scenario means we might write a duplicate test.
Read the actual test code, don't just guess from file names.
