# TestPilot

Claude Code skills for automated test generation. Finds coverage gaps and writes tests that add real value.

## Installation

```bash
git clone https://github.com/RanWurmbrand/test-pilot.git
cp -r test-pilot/.claude/skills/* ~/.claude/skills/
rm -rf test-pilot
```

Or as a one-liner:

```bash
git clone https://github.com/RanWurmbrand/test-pilot.git && cp -r test-pilot/.claude/skills/* ~/.claude/skills/ && rm -rf test-pilot
```

Restart Claude Code after installing.

## Usage

In any project with Claude Code:

```
/test-pilot                      # Full pipeline - auto-detects e2e vs unit tests

# E2E Testing
/find-e2e-opportunity            # Find gaps in e2e test coverage
/analyze-e2e-coverage <feature>  # Check existing e2e coverage
/plan-e2e-test <feature>         # Plan e2e tests
/write-e2e-test <feature>        # Write e2e test code

# Unit Testing
/find-unit-opportunity           # Find gaps in unit test coverage
/analyze-unit-coverage <feature> # Check existing unit coverage
/plan-unit-test <feature>        # Plan unit tests
/write-unit-test <feature>       # Write unit test code
```

## Skills

| Skill | Purpose |
|-------|---------|
| `test-pilot` | Main orchestrator - auto-detects test type, runs all phases with confirmations |
| `find-e2e-opportunity` | Find untested user workflows and UI interactions |
| `find-unit-opportunity` | Find untested functions and logic branches |
| `analyze-e2e-coverage` | Analyze existing e2e tests (Playwright, Cypress) |
| `analyze-unit-coverage` | Analyze existing unit tests (Jest, pytest) |
| `plan-e2e-test` | Design e2e test plan using page objects |
| `plan-unit-test` | Design unit test plan with mocks/fixtures |
| `write-e2e-test` | Write e2e test code following existing patterns |
| `write-unit-test` | Write unit test code following existing patterns |

## How It Works

1. **Find opportunities** - Scans issues, commits, docs, and code for untested areas
2. **Analyze coverage** - Reads existing tests to understand what's already covered
3. **Plan tests** - Creates test plan focusing only on gaps
4. **Write tests** - Generates code following existing patterns (never invents APIs)

Each phase asks for confirmation before proceeding.

## Key Features

- **No duplicate tests** - Analyzes existing coverage before writing
- **Follows your patterns** - Learns from existing tests in your codebase
- **Real APIs only** - Never invents methods, only uses what exists
- **Step-by-step control** - Asks for confirmation between phases
- **Dual-mode** - Supports both e2e and unit testing

## Requirements

- Claude Code v1.0.33 or later
