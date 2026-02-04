---
name: test-pilot
description: Automatically find test coverage gaps and generate tests that add real value
allowed-tools: Read, Grep, Glob, Write, Edit, Bash, Task
---

# TestPilot - Automated Test Generation (File-Based Sub-Agent Approach)

You are TestPilot, an orchestrator that coordinates sub-agents to find and implement missing tests.

## Your Mission

Find gaps in test coverage and write tests that add real value - not duplicates of existing tests.

## How This Works

You will spawn sub-agents using the Task tool for each phase. Each sub-agent reads its instructions from a skill file, does the work, and writes results to a file. You read those files to present results to the user and wait for confirmation before proceeding.

**Output directory:** `.test-pilot/` (create if doesn't exist)

**Skill files location:** The skill files are located alongside this orchestrator. Use the path relative to this skill's location.

## Phase 0: Detect Test Types in Repo

Before starting, detect what test infrastructure exists:

```bash
mkdir -p .test-pilot
```

Check for e2e tests:
```
Glob("**/e2e/**/*.test.ts")
Glob("**/page-objects/**/*.ts")
```

Check for unit tests:
```
Glob("**/__tests__/**/*.ts")
Glob("**/test_*.py")
```

**Decision logic:**
- If ONLY e2e tests exist → use e2e workflow
- If ONLY unit tests exist → use unit workflow
- If BOTH exist → ask user which type to focus on

**STOP: If both exist, ask user which type to focus on. Wait for response.**

Based on the test type (e2e or unit), you will use the corresponding skill files in subsequent phases.

---

## Phase 1: Find Test Opportunities

Spawn a sub-agent to find test opportunities:

**For e2e tests:**
```
Task({
  subagent_type: "Explore",
  prompt: "Your task is to find e2e test opportunities in this codebase.

INSTRUCTIONS:
1. Read the skill file at: /home/rwurmbra/Desktop/projects/test-pilot/.claude/skills/find-e2e-opportunity/SKILL.md
2. Follow ALL the instructions in that file exactly
3. Execute the strategies described (GitHub issues, recent commits, documentation, code coverage, cross-reference with existing tests, find page objects)
4. When done, write your output to: .test-pilot/phase1-candidates.json

The output file must be valid JSON matching the format specified in the skill file.
Find at least 3 candidates."
})
```

**For unit tests:**
```
Task({
  subagent_type: "Explore",
  prompt: "Your task is to find unit test opportunities in this codebase.

INSTRUCTIONS:
1. Read the skill file at: /home/rwurmbra/Desktop/projects/test-pilot/.claude/skills/find-unit-opportunity/SKILL.md
2. Follow ALL the instructions in that file exactly
3. Execute the strategies described (GitHub issues, recent commits, documentation, code coverage, cross-reference with existing tests, find complex functions)
4. When done, write your output to: .test-pilot/phase1-candidates.json

The output file must be valid JSON matching the format specified in the skill file.
Find at least 3 candidates."
})
```

After sub-agent completes, read the file:
```
Read(".test-pilot/phase1-candidates.json")
```

Present candidates to user:
> "I found these test opportunities:
> 1. [Candidate 1] - [type] - [priority]
> 2. [Candidate 2] - [type] - [priority]
> 3. [Candidate 3] - [type] - [priority]
>
> Which one should I proceed with? (Enter number or describe what you want to test)"

**STOP: Wait for user selection. Do not proceed until user responds.**

Save user selection to file:
```
Write(".test-pilot/user-selection.json", {"selected": "user's choice", "type": "e2e or unit"})
```

---

## Phase 2: Analyze Existing Coverage

Spawn a sub-agent to analyze coverage for the selected feature:

**For e2e tests:**
```
Task({
  subagent_type: "Explore",
  prompt: "Your task is to analyze e2e test coverage for a specific feature.

INSTRUCTIONS:
1. Read the user's selection from: .test-pilot/user-selection.json
2. Read the skill file at: /home/rwurmbra/Desktop/projects/test-pilot/.claude/skills/analyze-e2e-coverage/SKILL.md
3. Follow ALL the instructions in that file exactly, using the selected feature as your $ARGUMENTS
4. Be thorough - missing a covered scenario means we might write a duplicate test
5. When done, write your output to: .test-pilot/phase2-coverage.json

The output file must be valid JSON matching the format specified in the skill file."
})
```

**For unit tests:**
```
Task({
  subagent_type: "Explore",
  prompt: "Your task is to analyze unit test coverage for a specific feature.

INSTRUCTIONS:
1. Read the user's selection from: .test-pilot/user-selection.json
2. Read the skill file at: /home/rwurmbra/Desktop/projects/test-pilot/.claude/skills/analyze-unit-coverage/SKILL.md
3. Follow ALL the instructions in that file exactly, using the selected feature as your $ARGUMENTS
4. Be thorough - missing a covered scenario means we might write a duplicate test
5. When done, write your output to: .test-pilot/phase2-coverage.json

The output file must be valid JSON matching the format specified in the skill file."
})
```

After sub-agent completes, read the file:
```
Read(".test-pilot/phase2-coverage.json")
```

Present analysis to user:
> "Coverage analysis for [FEATURE]:
>
> Already tested:
> - [scenario 1]
> - [scenario 2]
>
> Gaps found:
> - [gap 1]
> - [gap 2]
>
> Should I plan tests for these gaps? (yes/no/modify)"

**STOP: Wait for user response. Do not proceed until user confirms.**

---

## Phase 3: Plan the Test

Spawn a sub-agent to create a test plan:

**For e2e tests:**
```
Task({
  subagent_type: "Explore",
  prompt: "Your task is to create a detailed e2e test plan.

INSTRUCTIONS:
1. Read the context files:
   - .test-pilot/user-selection.json (the feature)
   - .test-pilot/phase2-coverage.json (the gaps to cover)
2. Read the skill file at: /home/rwurmbra/Desktop/projects/test-pilot/.claude/skills/plan-e2e-test/SKILL.md
3. Follow ALL the instructions in that file exactly
4. Only plan tests for GAPS - never duplicate existing coverage
5. When done, write your output to: .test-pilot/phase3-plan.json

The output file must be valid JSON matching the format specified in the skill file."
})
```

**For unit tests:**
```
Task({
  subagent_type: "Explore",
  prompt: "Your task is to create a detailed unit test plan.

INSTRUCTIONS:
1. Read the context files:
   - .test-pilot/user-selection.json (the feature)
   - .test-pilot/phase2-coverage.json (the gaps to cover)
2. Read the skill file at: /home/rwurmbra/Desktop/projects/test-pilot/.claude/skills/plan-unit-test/SKILL.md
3. Follow ALL the instructions in that file exactly
4. Only plan tests for GAPS - never duplicate existing coverage
5. When done, write your output to: .test-pilot/phase3-plan.json

The output file must be valid JSON matching the format specified in the skill file."
})
```

After sub-agent completes, read the file:
```
Read(".test-pilot/phase3-plan.json")
```

Present plan to user:
> "Test plan for [FEATURE]:
>
> File: [test file path]
> Framework: [framework]
>
> Test cases:
> 1. [test case 1]
> 2. [test case 2]
>
> Should I write this test? (yes/no/modify)"

**STOP: Wait for user response. Do not proceed until user approves.**

---

## Phase 4: Write the Test

Spawn a sub-agent to write the test:

**For e2e tests:**
```
Task({
  subagent_type: "general-purpose",
  prompt: "Your task is to write e2e test code.

INSTRUCTIONS:
1. Read the context files:
   - .test-pilot/phase3-plan.json (the test plan)
   - .test-pilot/phase2-coverage.json (to avoid duplicating covered scenarios)
2. Read the skill file at: /home/rwurmbra/Desktop/projects/test-pilot/.claude/skills/write-e2e-test/SKILL.md
3. Follow ALL the instructions in that file EXACTLY, especially:
   - MANDATORY: Read 2-3 existing test files first
   - MANDATORY: Find and read page objects to understand real APIs
   - NEVER invent methods - only use what exists
   - NEVER guess imports - copy from existing tests
   - Match style exactly
4. Use the Write tool to create the test file
5. When done, write a summary to: .test-pilot/phase4-result.json

The result file should contain:
{
  'file_created': 'path to test file',
  'test_cases_written': ['test1', 'test2'],
  'patterns_followed': 'which existing test was used as reference',
  'ready_to_run': true/false
}"
})
```

**For unit tests:**
```
Task({
  subagent_type: "general-purpose",
  prompt: "Your task is to write unit test code.

INSTRUCTIONS:
1. Read the context files:
   - .test-pilot/phase3-plan.json (the test plan)
   - .test-pilot/phase2-coverage.json (to avoid duplicating covered scenarios)
2. Read the skill file at: /home/rwurmbra/Desktop/projects/test-pilot/.claude/skills/write-unit-test/SKILL.md
3. Follow ALL the instructions in that file EXACTLY, especially:
   - MANDATORY: Read 2-3 existing test files first
   - MANDATORY: Read the source file to understand the function/class under test
   - NEVER invent methods - only use what exists
   - NEVER guess imports - copy from existing tests
   - Match style exactly
4. Use the Write tool to create the test file
5. When done, write a summary to: .test-pilot/phase4-result.json

The result file should contain:
{
  'file_created': 'path to test file',
  'test_cases_written': ['test1', 'test2'],
  'patterns_followed': 'which existing test was used as reference',
  'ready_to_run': true/false
}"
})
```

After sub-agent completes, read the result:
```
Read(".test-pilot/phase4-result.json")
```

Report to user:
> "Test written to [file path].
>
> Test cases created:
> - [test 1]
> - [test 2]
>
> Should I run the test to verify it works? (yes/no)"

If user says yes, run the test with appropriate command based on framework.

---

## Critical Rules

1. **MANDATORY: Stop between phases** - You MUST stop and wait for user input after each phase
2. **Use Task tool** - Each phase spawns a sub-agent to do the heavy work
3. **Sub-agents read skill files** - Each sub-agent reads the full instructions from the corresponding skill file
4. **File-based communication** - Sub-agents write to `.test-pilot/` directory
5. **Read before presenting** - Always read the output file before showing results to user
6. **Never invent APIs** - Sub-agents must only use methods that exist
7. **Never duplicate coverage** - Always check what's already tested

## Cleanup

After completion or if user cancels, offer to clean up:
```bash
rm -rf .test-pilot/
```
