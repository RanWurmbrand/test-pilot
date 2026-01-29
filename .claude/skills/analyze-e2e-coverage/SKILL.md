---
name: analyze-e2e-coverage
description: Analyze existing e2e tests to understand what's already covered and identify gaps
argument-hint: <feature-name>
allowed-tools: Read, Grep, Glob
---

# Analyze E2E Coverage

You are a coverage analyst. Your job is to understand what's already tested so we don't write duplicate tests.

This skill focuses on **e2e (end-to-end) test coverage** - UI workflows, page interactions, user journeys.

## Input

You will receive a feature name or description via $ARGUMENTS.

Feature to analyze: $ARGUMENTS

## Process

### Step 1: Find Related Test Files

**Extract keywords from the feature name.** Remove common words like "the", "a", "test", "add", "implement", "create", "new". Use the remaining meaningful keywords.

Search for tests by filename:
```
Glob("**/test*{keyword}*.py")
Glob("**/*{keyword}*.test.ts")
Glob("**/*{keyword}*.spec.ts")
```

**E2E specific - also search in e2e folders:**
```
Glob("**/e2e/**/*{keyword}*.test.ts")
Glob("**/e2e/**/*{keyword}*.spec.ts")
```

Search for tests by content using grep:
```bash
grep -r -l -i -E "keyword1|keyword2" --include="*.test.ts" --include="*.spec.ts" --include="*_test.py" --include="test_*.py"
```

Also use the Grep tool:
```
Grep("{keyword}", "**/*.test.ts")
Grep("{keyword}", "**/test_*.py")
```

### Step 2: Read and Analyze Test Files

For each related test file, use Read to examine it.

Extract in this structure:
- **tested_functions**: list of {name, file} - what functions/methods have tests
- **covered_scenarios**: list of {scenario description, test_name, file, assertions array} - what behaviors are verified
- **edge_cases_covered**: list of edge case descriptions
- **error_cases_covered**: list of error conditions tested

**E2E specific - also extract:**
- **page_objects_used**: list of page objects referenced in tests
- **ui_interactions_tested**: list of UI actions (clicks, fills, navigations)
- **user_flows_covered**: list of complete user journeys tested
- **selectors_used**: patterns of selectors used (data-testid, css, etc.)
- **waits_used**: how tests wait for elements/states

Be exhaustive - list every distinct scenario you can identify.
Focus on WHAT behavior is tested, not HOW it's implemented.

### Step 3: Identify Gaps

Compare what SHOULD be tested vs what IS tested.

Common gaps to look for:
- Happy path tested but error cases missing
- Basic case tested but edge cases missing
- Function tested but not all branches covered
- Integration points not tested

**E2E specific gaps to look for:**
- User flows documented but not tested end-to-end
- Page interactions that exist in page objects but aren't tested
- Error states in UI not verified
- Navigation paths not covered
- Form validations not tested through UI

## Output Format

```json
{
  "test_files_analyzed": [
    "tests/e2e/auth/login.test.ts",
    "tests/e2e/auth/logout.test.ts"
  ],
  "covered_scenarios": [
    {
      "scenario": "successful login with valid credentials",
      "test_name": "test_login_success",
      "file": "tests/e2e/auth/login.test.ts",
      "assertions": ["user is authenticated", "dashboard is shown"]
    }
  ],
  "page_objects_used": [
    "LoginPage",
    "DashboardPage"
  ],
  "ui_interactions_tested": [
    "fill username field",
    "fill password field",
    "click login button"
  ],
  "user_flows_covered": [
    "login -> dashboard",
    "logout -> login page"
  ],
  "edge_cases_covered": [
    "empty password shows error",
    "very long username is truncated"
  ],
  "error_cases_covered": [
    "invalid credentials shows error message",
    "account locked shows lock message"
  ],
  "gaps": [
    {
      "scenario": "login with expired session token",
      "priority": "high",
      "why_important": "security edge case not covered in UI"
    },
    {
      "scenario": "forgot password flow",
      "priority": "medium",
      "why_important": "user flow exists but no e2e test"
    }
  ],
  "recommendation": "Focus on session handling and password recovery flows"
}
```

## Critical Rule

Be thorough. Missing a covered scenario means we might write a duplicate test.
Read the actual test code, don't just guess from file names.
