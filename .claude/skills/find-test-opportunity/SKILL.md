---
name: find-test-opportunity
description: Find areas in the codebase that need test coverage
allowed-tools: Read, Grep, Glob, Bash
---

# Find Test Opportunity

You are a test opportunity scout. Your job is to find areas in the codebase that need test coverage.

## Strategies

### 1. GitHub Issues

```bash
gh issue list --json number,title,labels,assignees --limit 20
```

Look for:
- Unassigned issues
- Issues with labels like "bug", "test", "coverage"
- Issues that describe testable behavior

### 2. Documentation Gaps

Use Glob to find docs:
```
Glob("**/*.md")
Glob("**/docs/**/*")
```

Read the docs and identify:
- Features described but possibly not tested
- API endpoints mentioned
- User workflows documented

### 3. Code Coverage

Use Glob to find source files:
```
Glob("**/*.py")
Glob("**/*.ts")
Glob("**/*.js")
```

Look for:
- Complex functions with many branches
- Error handling code
- Recently modified files (check git log)

### 4. Cross-reference with Existing Tests

Use Glob to find test files:
```
Glob("**/test*.py")
Glob("**/*.test.ts")
Glob("**/*.spec.ts")
```

Compare what's tested vs what exists in source.

## Output Format

Return a ranked list:

```json
{
  "candidates": [
    {
      "name": "User authentication flow",
      "type": "e2e",
      "source": "github_issue",
      "priority": "high",
      "difficulty": "medium",
      "justification": "Issue #42 reports login bugs, no e2e test exists"
    },
    {
      "name": "validateInput function",
      "type": "unit",
      "source": "code_coverage",
      "priority": "medium",
      "difficulty": "easy",
      "justification": "Complex validation logic with no test coverage"
    }
  ]
}
```

## Selection Criteria

Prioritize candidates that are:
1. High impact (critical functionality)
2. Easy to test (clear inputs/outputs)
3. Not already covered (check existing tests first)
4. Recently active (not deprecated code)
