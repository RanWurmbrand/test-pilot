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

## Architecture

TestPilot uses a **sub-agent orchestration** pattern:

1. The main `/test-pilot` skill acts as an orchestrator
2. For each phase, it spawns a dedicated sub-agent using the Task tool
3. Each sub-agent has its own clean context window
4. Sub-agents read the full skill instructions and write results to files
5. The orchestrator reads these files and presents results to the user

### Why Sub-Agents?

Agents have a context window - what they read and remember during a session. This window has a limit, and quality can degrade as it fills up.

TestPilot reads project docs, searches GitHub issues, and browses the codebase. These steps add a lot to the context window.

By using sub-agents with separate context windows, the heavy searching happens in isolation. Only the results come back to the main agent. This keeps the main context clean and focused, maintaining consistent quality throughout the session.

### File-Based Communication

Sub-agents write results to `.test-pilot/` in your project directory:

- `phase1-candidates.json` - Test opportunities found
- `user-selection.json` - What you selected to test
- `phase2-coverage.json` - Coverage analysis and gaps
- `phase3-plan.json` - Detailed test plan
- `phase4-result.json` - Final result after writing tests

This directory is cleaned up after completion.

## Skills

| Skill | Purpose |
|-------|---------|
| `test-pilot` | Orchestrator - spawns sub-agents, manages phases, handles user confirmations |
| `find-e2e-opportunity` | Find untested user workflows and UI interactions |
| `find-unit-opportunity` | Find untested functions and logic branches |
| `analyze-e2e-coverage` | Analyze existing e2e tests (Playwright, Cypress) |
| `analyze-unit-coverage` | Analyze existing unit tests (Jest, pytest) |
| `plan-e2e-test` | Design e2e test plan using page objects |
| `plan-unit-test` | Design unit test plan with mocks/fixtures |
| `write-e2e-test` | Write e2e test code following existing patterns |
| `write-unit-test` | Write unit test code following existing patterns |

## How It Works

1. **Phase 0: Detect** - Identifies if project has e2e tests, unit tests, or both
2. **Phase 1: Find opportunities** - Sub-agent scans issues, commits, docs for untested areas
3. **Phase 2: Analyze coverage** - Sub-agent reads existing tests to find gaps
4. **Phase 3: Plan tests** - Sub-agent creates test plan focusing only on gaps
5. **Phase 4: Write tests** - Sub-agent generates code following existing patterns

Each phase asks for confirmation before proceeding.

## Key Features

- **No duplicate tests** - Analyzes existing coverage before writing
- **Follows your patterns** - Learns from existing tests in your codebase
- **Real APIs only** - Never invents methods, only uses what exists
- **Step-by-step control** - Asks for confirmation between phases
- **Dual-mode** - Supports both e2e and unit testing
- **Clean context** - Sub-agents keep the main conversation focused

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
