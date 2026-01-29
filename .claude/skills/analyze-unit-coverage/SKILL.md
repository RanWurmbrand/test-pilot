---
name: analyze-unit-coverage
description: Analyze existing unit tests to understand what's already covered and identify gaps
argument-hint: <feature-name>
allowed-tools: Read, Grep, Glob
---

# Analyze Unit Coverage

You are a coverage analyst. Your job is to understand what's already tested so we don't write duplicate tests.

This skill focuses on **unit test coverage** - individual functions, methods, classes in isolation.

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

**Unit specific - also search in unit folders:**
```
Glob("**/unit/**/*{keyword}*.test.ts")
Glob("**/unit/**/*{keyword}*.spec.ts")
Glob("**/test_{keyword}*.py")
Glob("**/*{keyword}_test.py")
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

**Unit specific - also extract:**
- **mocks_used**: list of mocked dependencies
- **fixtures_used**: list of fixtures/setup used
- **input_variations_tested**: different inputs tested
- **branches_covered**: conditional branches verified
- **return_values_checked**: what return values are asserted

Be exhaustive - list every distinct scenario you can identify.
Focus on WHAT behavior is tested, not HOW it's implemented.

### Step 3: Identify Gaps

Compare what SHOULD be tested vs what IS tested.

Common gaps to look for:
- Happy path tested but error cases missing
- Basic case tested but edge cases missing
- Function tested but not all branches covered
- Integration points not tested

**Unit specific gaps to look for:**
- Functions with no tests at all
- Branches (if/else, switch) not covered
- Edge case inputs not tested (null, empty, boundary values)
- Error conditions not verified (exceptions, error returns)
- Mock interactions not verified

## Output Format

```json
{
  "test_files_analyzed": [
    "tests/unit/test_validators.py",
    "tests/unit/validators.test.ts"
  ],
  "covered_scenarios": [
    {
      "scenario": "validates email format correctly",
      "test_name": "test_email_validation",
      "file": "tests/unit/test_validators.py",
      "assertions": ["returns true for valid email", "returns false for invalid"]
    }
  ],
  "tested_functions": [
    {"name": "validate_email", "file": "src/validators.py"},
    {"name": "validate_phone", "file": "src/validators.py"}
  ],
  "mocks_used": [
    "mock_database",
    "mock_api_client"
  ],
  "fixtures_used": [
    "sample_user",
    "valid_credentials"
  ],
  "input_variations_tested": [
    "valid email",
    "invalid email without @",
    "empty string"
  ],
  "edge_cases_covered": [
    "empty string input",
    "very long input"
  ],
  "error_cases_covered": [
    "null input raises ValueError",
    "malformed data raises ParseError"
  ],
  "gaps": [
    {
      "scenario": "validate_email with unicode characters",
      "priority": "medium",
      "why_important": "international email addresses not tested"
    },
    {
      "scenario": "validate_phone with country codes",
      "priority": "high",
      "why_important": "function exists but no tests for country code handling"
    }
  ],
  "recommendation": "Focus on international input handling and country code validation"
}
```

## Critical Rule

Be thorough. Missing a covered scenario means we might write a duplicate test.
Read the actual test code, don't just guess from file names.
