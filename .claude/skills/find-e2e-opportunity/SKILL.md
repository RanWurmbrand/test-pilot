# Find E2E Test Opportunity

You are a test opportunity scout. Your job is to find areas in the codebase that need **e2e (end-to-end) test coverage**.

E2E tests verify complete user workflows through the UI - login flows, checkout processes, navigation, form submissions, etc.

## Strategies

### 1. GitHub Issues

```bash
gh issue list --state open --json number,title,labels,assignees,state --limit 20
```

**Filter for issues that are OPEN and have no assignees.** Skip assigned or closed issues.

Look for:
- Issues with labels like "bug", "test", "coverage"
- Issues that describe testable behavior
- Issues that don't require significant new feature work
- **E2E specific**: Issues about UI bugs, user workflows, navigation, forms, buttons, pages

### 2. Recent Commits

```bash
git log --since="3 months ago" --pretty=format:"%s" --no-merges | head -20
```

Look for:
- New features added recently
- Bug fixes that might need regression tests
- Refactored code that should be verified
- Areas with active development
- **E2E specific**: Commits mentioning UI, pages, views, buttons, forms, navigation, user flows

Cross-reference commits with existing tests:
```bash
git log --since="3 months ago" --name-only --pretty=format: | grep -E "\.(ts|js|py)$" | sort | uniq -c | sort -rn | head -10
```

This shows which files changed most - prioritize testing actively developed code.

### 3. Documentation Gaps

Use Glob to find docs:
```
Glob("**/*.md")
Glob("**/docs/**/*")
```

**Only consider docs modified in the last 2 months.** Check modification date:
```bash
git log -1 --format=%cd --date=short -- <file>
```
Skip docs older than 2 months - they may describe deprecated features.

**Cross-reference with recent commits.** Prioritize features that appear in commit messages from the last 2 months. Avoid features that seem deprecated or removed.

Read recent docs and identify:
- Features described but possibly not tested
- API endpoints mentioned
- User workflows documented
- Features that appear in recent commit messages (actively maintained)
- **E2E specific**: User guides, walkthrough docs, UI feature descriptions, navigation flows

### 4. Code Coverage

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
- **E2E specific**: Page components, views, UI handlers, form validators, navigation controllers

### 5. Cross-reference with Existing Tests

Use Glob to find test files:
```
Glob("**/test*.py")
Glob("**/*.test.ts")
Glob("**/*.spec.ts")
```

**E2E specific - also check for existing e2e tests:**
```
Glob("**/e2e/**/*.test.ts")
Glob("**/e2e/**/*.spec.ts")
```

Compare what's tested vs what exists in source.

### 6. Find Page Objects (E2E Specific)

```
Glob("**/page-objects/**/*.ts")
Glob("**/pages/**/*.ts")
Grep("class.*Page", "**/*.ts")
```

Page objects reveal what UI interactions are available and what might need testing.

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
      "name": "Settings page navigation",
      "type": "e2e",
      "source": "docs_coverage",
      "priority": "medium",
      "difficulty": "easy",
      "justification": "Documented in user guide but no e2e test covers it"
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
5. **E2E specific**: User-facing, involves UI interaction, represents complete workflow
