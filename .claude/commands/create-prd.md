Generate a Product Requirements Document through structured interview, then configure the project workspace.

**Before asking questions:**
Check if any non-markdown, non-config source files exist in the project (e.g., .py, .ts, .js, .go files or src/ directories). If they do, use a subagent to briefly summarize the existing codebase structure — this prevents asking questions the code already answers.

---

## Phase 1 — Product Q&A

**Ask these questions ONE AT A TIME. Wait for the answer before continuing.**

1. "What problem does this app solve? Who experiences this problem day-to-day?"

2. "Who are the target users? Be specific — not 'developers' but 'junior Python developers who are new to testing'."

3. "What are the 3-5 core features of the MVP? List them briefly — I'll ask follow-up questions about each."

4. For each feature the user listed in Q3: if they did NOT already describe the simplest version or success criteria for that feature, ask: "For [feature name]: What's the simplest version of this that would still be useful? What does success look like?" — Skip this question for any feature where Q3 already provided this detail.

5. "What is explicitly OUT of scope for this first version? What are you deliberately not building yet?"

6. "How will you demo this? What does a successful 5-minute demo look like?"

---

## Phase 2 — Tech Stack Discussion

After collecting all product answers, recommend a tech stack based on the problem domain and features. Present your recommendation as:

~~~~
Based on what you've described, here's what I'd recommend:

**Language:** [e.g. Python 3.11+] — [one-sentence reason]
**Framework:** [e.g. FastAPI] — [one-sentence reason]
**Testing:** [e.g. pytest + httpx] — [one-sentence reason]
**Linter:** [e.g. ruff] — [one-sentence reason]
**Other key dependencies:** [e.g. SQLite + SQLAlchemy for storage] — [one-sentence reason]

Does this stack work for you, or would you like to swap anything out?
~~~~

Wait for confirmation or adjustments before continuing. If the user swaps something, acknowledge it and confirm the final stack.

Then ask ONE AT A TIME:

7. "What naming convention do you prefer? (e.g. snake_case for Python, camelCase for JS, or mixed)"

8. "What commit style do you prefer? Conventional Commits (feat/fix/docs/test) or simple past-tense ('Added login', 'Fixed bug')?"

---

## Phase 3 — Write Files

After all answers are confirmed, write the following files:

### A. `.claude/PRD.md`

~~~~markdown
# Product Requirements Document
**Project:** [project name]
**Created:** [today's date]

## Overview
**Problem:** [problem statement]
**Target Users:** [specific user description]
**Mission:** [one sentence: "A [what it is] that helps [who] to [do what]."]

## MVP Scope

### In Scope
- [feature 1]
- [feature 2]
- [feature 3]

### Out of Scope (V1)
- [explicitly excluded item 1]
- [explicitly excluded item 2]

## Core Features

### Feature 1: [name]
**Description:** [what it does]
**User Story:** As a [user], I want to [action] so that [outcome].
**Acceptance Criteria:**
- [ ] [specific, testable criterion]
- [ ] [specific, testable criterion]

[Repeat for each feature]

## Success Criteria
- [ ] [measurable outcome that proves the MVP works]
- [ ] [another measurable outcome]

## Implementation Phases
[Suggest 2-4 phases based on the features, ordered by dependency]

**Phase 1 — [name]:** [what this phase delivers]
**Phase 2 — [name]:** [what this phase delivers]

## Architecture Notes
**Tech Stack:** [confirmed stack from Phase 2]
**Key Decisions:** [any decisions implied by the requirements]
~~~~

### B. `CLAUDE.md`

Overwrite the root `CLAUDE.md` with project-specific values:

~~~~markdown
# [Project Name]

[Project description — one sentence.]

## Commands
| Command | Purpose |
|---------|---------|
| `/create-prd` | Interview → PRD + project setup |
| `/plan-feature` | Generate implementation plans from PRD |
| `/execute <plan-file>` | TDD step-by-step execution of a plan |
| `/test [path]` | Run tests and triage failures |
| `/commit [message]` | Stage and commit with conventional message |
| `/review [plan-file]` | Review implementation against plan + PRD |
| `/prime` | Load project context at session start |
| `/help` | Show command reference |

## Tech Stack
- **Language:** [confirmed language + version]
- **Framework:** [confirmed framework]
- **Testing:** [confirmed test framework]
- **Linter:** [confirmed linter, or "none" if not set]

## Key Directories
- `src/` — application source code
- `tests/` — test files (mirror src/ structure)
- `.agents/plans/` — implementation plans
- `.claude/` — Claude Code config and memory

## Test Command
```
[exact test command, e.g. pytest tests/ -v]
```

## Lint Command
```
[exact lint command, e.g. ruff check . or "none"]
```

## Naming Convention
[e.g. snake_case for files and functions, PascalCase for classes]

## Commit Style
[e.g. Conventional Commits: feat/fix/docs/test/refactor/chore]
~~~~

### C. `.claude/memory/preferences/testing_style.md`

~~~~markdown
---
name: Testing Style Preferences
description: How tests should be written for this project
type: user
---

## Framework
[Framework from confirmed stack, e.g. pytest]

## TDD Rules
1. Write the failing test before any implementation
2. Run the test to confirm it fails for the right reason
3. Write only enough code to make the test pass
4. Never modify a test to make it pass — fix the implementation
5. Each test covers one specific behavior

## What to Test
- Happy path (expected inputs → expected outputs)
- Failure cases (invalid input, missing data, edge cases)
- Boundary conditions

## What NOT to Test
- Internal implementation details
- Third-party library behavior
- Private/helper functions directly

## Fixture Patterns
[Language-appropriate fixture pattern, e.g. pytest fixtures for Python, beforeEach for JS]
~~~~

### D. `.claude/memory/preferences/coding_style.md`

~~~~markdown
---
name: Coding Style Preferences
description: Naming, structure, and patterns for this project
type: user
---

## Naming
[Confirmed naming convention, e.g. snake_case functions/variables, PascalCase classes]

## File Structure
- Group files by domain (e.g. auth/, users/, products/) not by type
- One clear responsibility per file
- Test files mirror source structure under tests/

## Error Handling
- Raise specific exceptions, not generic ones
- Validate at system boundaries (API endpoints, CLI input)
- Don't add defensive checks for conditions that can't happen internally

## Code Patterns
- Functions do one thing
- No functions longer than 30 lines
- No hardcoded values — use constants or config
- No commented-out code

## What to Avoid
- Premature abstractions
- Unused parameters
- Magic numbers
~~~~

### E. `.claude/memory/preferences/commit_style.md`

~~~~markdown
---
name: Commit Style Preferences
description: How commits should be structured for this project
type: user
---

## Style
[Confirmed commit style]

## Conventional Format (if using Conventional Commits)
```
<type>: <short description>

Types: feat | fix | docs | test | refactor | chore
```

## Simple Format (if using simple past-tense)
```
Added login endpoint
Fixed null pointer in user lookup
```

## Rules
- Subject line: 50 chars max, imperative mood
- No period at end
- One logical change per commit
- Never commit secrets, credentials, or .env files
~~~~

### F. Verify `.claude/memory/MEMORY.md` still has its index header (don't overwrite — only check it exists).

---

## Phase 4 — Confirmation Output

After writing all files, output:

~~~~
✅ PRD written to .claude/PRD.md
✅ CLAUDE.md configured for [project name]
✅ Memory preferences initialized

Tech stack locked in:
- Language: [language]
- Framework: [framework]
- Testing: [test command]
- Linter: [lint command]

Phases identified:
- Phase 1: [name]
- Phase 2: [name]
[...]

Next: Run `/plan-feature` to generate implementation plans for all phases.
~~~~
