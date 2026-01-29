# TestPilot

Claude Code skills for automated test generation. Finds coverage gaps and writes tests that add real value.

**Requires [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)**

## Installation

```bash
# Clone to a permanent location
git clone https://github.com/RanWurmbrand/test-pilot.git ~/.test-pilot

# Create skills directory if needed
mkdir -p ~/.claude/skills

# Symlink the skills
ln -s ~/.test-pilot/.claude/skills/* ~/.claude/skills/
```

Restart Claude Code after installing.

## Updating

```bash
cd ~/.test-pilot && git pull
```

## Uninstalling

```bash
# Remove symlinks
rm -rf ~/.claude/skills/test-pilot ~/.claude/skills/analyze-e2e-coverage \
   ~/.claude/skills/analyze-unit-coverage ~/.claude/skills/find-e2e-opportunity \
   ~/.claude/skills/find-unit-opportunity ~/.claude/skills/plan-e2e-test \
   ~/.claude/skills/plan-unit-test ~/.claude/skills/write-e2e-test \
   ~/.claude/skills/write-unit-test

# Remove the repo
rm -rf ~/.test-pilot
```

## Usage

In any project with Claude Code:

```
/test-pilot                       # Full pipeline - auto-detects e2e vs unit tests

# E2E Testing
/find-e2e-opportunity             # Find gaps in e2e test coverage
/analyze-e2e-coverage <feature>   # Check existing e2e coverage for a feature
/plan-e2e-test <feature>          # Plan e2e tests for a feature
/write-e2e-test                   # Write e2e test code (uses context from previous phases)

# Unit Testing
/find-unit-opportunity            # Find gaps in unit test coverage
/analyze-unit-coverage <feature>  # Check existing unit coverage for a feature
/plan-unit-test <feature>         # Plan unit tests for a feature
/write-unit-test                  # Write unit test code (uses context from previous phases)
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

## Supported Frameworks

**E2E**: Playwright, Cypress, WebDriverIO, Selenium

**Unit**: Jest, Mocha, Vitest, pytest, unittest

## Contributing

Contributions welcome! Open to any improvements, bug fixes, or new ideas.

Some areas I'd especially love help with:

- **Framework-specific skills** - e.g., `/write-playwright-test`, `/write-pytest-test`
- **Language-specific skills** - e.g., `/find-go-opportunity`, `/write-rust-test`
- **Test type skills** - e.g., `/write-integration-test`, `/write-api-test`

New skills should follow the existing SKILL.md format in `.claude/skills/`.

PRs welcome at [github.com/RanWurmbrand/test-pilot](https://github.com/RanWurmbrand/test-pilot).

