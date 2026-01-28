# TestPilot

Claude Code skills for automated test generation. Finds coverage gaps and writes tests that add real value.

## Installation

Copy skills to your personal Claude folder:

```bash
cp -r .claude/skills/* ~/.claude/skills/
```

## Usage

In any project with Claude Code:

```
/test-pilot              # Run full pipeline with confirmations
/find-test-opportunity   # Find what needs testing
/analyze-coverage login  # Check existing coverage for "login"
/plan-test auth          # Plan tests for "auth" feature
/write-test session      # Write tests for "session"
```

## Skills

| Skill | Purpose |
|-------|---------|
| `test-pilot` | Main orchestrator - runs all phases with confirmation between steps |
| `find-test-opportunity` | Phase 1: Find untested areas (GitHub issues, docs, code) |
| `analyze-coverage` | Phase 2: Analyze existing tests to avoid duplication |
| `plan-test` | Phase 3: Design test plan for identified gaps |
| `write-test` | Phase 4: Write actual test code using existing patterns |

## How It Works

1. **Find opportunities** - Scans issues, docs, and code for untested areas
2. **Analyze coverage** - Reads existing tests to understand what's already covered
3. **Plan tests** - Creates test plan focusing only on gaps
4. **Write tests** - Generates code following existing patterns (never invents APIs)

## Key Features

- **No duplicate tests** - Analyzes existing coverage before writing
- **Follows your patterns** - Learns from existing tests in your codebase
- **Real APIs only** - Never invents methods, only uses what exists
- **Step-by-step control** - Asks for confirmation between phases
