# AI Development Harness — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a reusable AI development harness for Claude Code — a GitHub template repo with slash commands, persistent memory, and a TDD-gated execution workflow that automates the full development lifecycle.

**Architecture:** The harness is entirely markdown files interpreted by Claude Code: 9 command files (`.claude/commands/`), 7 memory files (`.claude/memory/`), a CLAUDE.md, a README, and a placeholder for future phase plans (`.agents/plans/`). No application code is written here — the harness is the deliverable.

**Tech Stack:** Claude Code slash commands (markdown), git, shell commands for verification

**Note on TDD:** This project has no unit-testable code — commands are markdown instruction files. TDD here means: define acceptance criteria for each command before writing it, write the file, verify the command produces expected behavior by inspecting output, fix if needed, then commit.

---

## File Map

| File | Responsibility |
|---|---|
| `CLAUDE.md` | Project context, available commands list, code style rules, test/lint commands (filled in by `/init-project`) |
| `README.md` | How to fork and use the harness, command reference, demo walkthrough |
| `.claude/PRD.md` | Placeholder — written live by `/create-prd` during demo |
| `.claude/commands/help.md` | Lists all commands with descriptions |
| `.claude/commands/init-project.md` | One-time setup after forking: writes CLAUDE.md + memory files |
| `.claude/commands/prime.md` | Loads project context from memory + CLAUDE.md + PRD into a fresh session |
| `.claude/commands/create-prd.md` | Structured Q&A → writes PRD.md |
| `.claude/commands/plan-feature.md` | Analyzes codebase via subagents → writes phase plan to `.agents/plans/` |
| `.claude/commands/execute.md` | Full TDD cycle per plan step: write tests → implement → run tests → lint → commit |
| `.claude/commands/test.md` | Run test suite, fix failures, re-run |
| `.claude/commands/commit.md` | Semantic git commit using project's commit style |
| `.claude/commands/review.md` | Phase review: tests, lint, PRD alignment, structured report |
| `.claude/memory/MEMORY.md` | Memory index — one pointer per memory file |
| `.claude/memory/user_profile.md` | Developer role, background, preferences (template) |
| `.claude/memory/project_decisions.md` | Key decisions + rationale (template) |
| `.claude/memory/feedback_corrections.md` | Session learnings, what worked/didn't (template) |
| `.claude/memory/preferences/testing_style.md` | Test framework conventions (template — filled by `/init-project`) |
| `.claude/memory/preferences/coding_style.md` | Naming, structure, error handling conventions (template) |
| `.claude/memory/preferences/commit_style.md` | Commit message format and tag preferences |
| `.agents/plans/.gitkeep` | Keeps plans directory in git before any plans are generated |
| `docs/architecture.md` | Human-readable overview of the harness system |

---

## Task 1: Initialize Git Repo and Project Skeleton

**Files:**
- Create: `CLAUDE.md` (initial placeholder — overwritten by `/init-project`)
- Create: `.gitignore`
- Create: `.agents/plans/.gitkeep`
- Create: `docs/architecture.md`

**Acceptance criteria:** `git status` shows a clean commit. All directories exist.

- [ ] **Step 1.1: Initialize git repo**

```bash
cd /Users/rohan.kantaria/Desktop/genAI/capstone_project
git init
```

Expected output: `Initialized empty Git repository in .../capstone_project/.git/`

- [ ] **Step 1.2: Create directory structure**

```bash
mkdir -p .claude/commands .claude/memory/preferences .agents/plans docs/superpowers/specs docs/superpowers/plans
touch .agents/plans/.gitkeep
```

Expected: no errors. Verify with `ls -la .claude/ .agents/plans/`

- [ ] **Step 1.3: Create `.gitignore`**

Create `.gitignore` with this exact content:

```
# Python
__pycache__/
*.py[cod]
*.egg-info/
.venv/
venv/
dist/
.env

# Node
node_modules/
.next/
.nuxt/
dist/

# OS
.DS_Store
Thumbs.db

# IDE
.vscode/settings.json
.idea/

# Secrets
*.env
.env.local
.env.production
```

- [ ] **Step 1.4: Create initial `CLAUDE.md` placeholder**

Create `CLAUDE.md` with this content:

```markdown
# CLAUDE.md — Project Context

> This file is populated by running `/init-project` after forking this template.
> Until then, this is a placeholder.

## Project
- **Name:** [run /init-project to fill this in]
- **Description:** [run /init-project to fill this in]

## Commands
All available slash commands for this project:

| Command | What it does |
|---|---|
| `/help` | List all commands with descriptions |
| `/init-project` | One-time setup after forking — fills in this file |
| `/prime` | Load project context into a fresh Claude session |
| `/create-prd` | Generate a PRD through structured Q&A |
| `/plan-feature [name]` | Analyze codebase → create phase implementation plan |
| `/execute [plan-path]` | Run TDD cycle for each step in a plan |
| `/test` | Run tests, fix failures, re-run |
| `/commit` | Create semantic git commit |
| `/review` | Review completed phase before advancing |

## Tech Stack
- **Language:** [run /init-project]
- **Framework:** [run /init-project]
- **Database:** [run /init-project]
- **Test command:** [run /init-project] (e.g., `pytest`, `npm test`)
- **Lint command:** [run /init-project] (e.g., `ruff check . --fix`, `npm run lint`)

## Code Style
- **Naming:** [run /init-project]
- **File structure:** [run /init-project]

## Key Directories
- `.claude/commands/` — slash command definitions
- `.claude/memory/` — persistent memory files
- `.agents/plans/` — generated phase implementation plans
- `.claude/PRD.md` — product requirements (generated by /create-prd)
```

- [ ] **Step 1.5: Create `docs/architecture.md`**

Create `docs/architecture.md` with this content:

```markdown
# AI Development Harness — Architecture

## What This Is

A reusable Claude Code harness that automates the full development lifecycle:
PRD → Phase Plans → TDD Implementation → Review → Commit

## How It Works

### Session Start
Run `/prime` to load project context from memory files, CLAUDE.md, PRD, and the latest phase plan. This prevents agents from asking questions already answered in project docs.

### Requirements Phase
Run `/create-prd` to define what you're building. Claude asks structured questions and writes a PRD to `.claude/PRD.md`.

### Planning Phase
Run `/plan-feature [feature]` to generate a step-by-step phase plan. Three subagents analyze the codebase in parallel (structure, dependencies, risks). Plan is written to `.agents/plans/`.

### Implementation Phase
Run `/execute [plan-path]` to implement the plan. Each step follows the TDD cycle:
1. Write failing tests
2. Implement to make tests pass
3. Run lint/formatter
4. Commit with semantic message
5. Advance to next step

### Review Phase
Run `/review` to validate the completed phase against the PRD and test coverage before advancing.

## Memory System

Memory files persist across Claude sessions. They are NOT auto-loaded (keeps context clean). `/prime` reads the index (`MEMORY.md`) and loads relevant files on demand.

```
.claude/memory/
├── MEMORY.md                    # Index — one pointer per file
├── user_profile.md              # Developer background + preferences
├── project_decisions.md         # Technical decisions + rationale
├── feedback_corrections.md      # Session learnings
└── preferences/
    ├── testing_style.md         # Test framework patterns
    ├── coding_style.md          # Naming + structure conventions
    └── commit_style.md          # Commit message format
```

## Reuse

This repo is a GitHub template. Fork it → run `/init-project` → start building.
The harness is app-agnostic. It adapts to any language/framework via `/init-project`.
```

- [ ] **Step 1.6: Verify structure**

```bash
find /Users/rohan.kantaria/Desktop/genAI/capstone_project -not -path '*/.git/*' | sort
```

Expected output should show all directories and the 4 files created so far.

- [ ] **Step 1.7: Initial commit**

```bash
git add .gitignore CLAUDE.md docs/ .agents/
git commit -m "chore: initialize harness project structure"
```

---

## Task 2: Memory System Files

**Files:**
- Create: `.claude/memory/MEMORY.md`
- Create: `.claude/memory/user_profile.md`
- Create: `.claude/memory/project_decisions.md`
- Create: `.claude/memory/feedback_corrections.md`
- Create: `.claude/memory/preferences/testing_style.md`
- Create: `.claude/memory/preferences/coding_style.md`
- Create: `.claude/memory/preferences/commit_style.md`

**Acceptance criteria:** All files exist and contain template content that is clear enough to fill in without further instruction.

- [ ] **Step 2.1: Create `MEMORY.md` (index)**

Create `.claude/memory/MEMORY.md` with this content:

```markdown
# Memory Index

This file is the index. Load specific memory files only when relevant — do not load all at session start.

## Files

- [user_profile.md](user_profile.md) — Developer role, background, tech experience, collaboration preferences
- [project_decisions.md](project_decisions.md) — Key technical decisions made and why (updated during /execute)
- [feedback_corrections.md](feedback_corrections.md) — What worked/didn't in past sessions (update after each session)
- [preferences/testing_style.md](preferences/testing_style.md) — Test framework, TDD conventions, fixture patterns
- [preferences/coding_style.md](preferences/coding_style.md) — Naming conventions, file structure, error handling
- [preferences/commit_style.md](preferences/commit_style.md) — Commit message format, tag conventions

## When to Load Each File

| Situation | Load |
|---|---|
| Starting any session | user_profile.md (via /prime) |
| Writing tests | preferences/testing_style.md |
| Writing implementation code | preferences/coding_style.md |
| Creating a commit | preferences/commit_style.md |
| Resuming previous work | project_decisions.md |
| Debugging unexpected behavior | feedback_corrections.md |
```

- [ ] **Step 2.2: Create `user_profile.md`**

Create `.claude/memory/user_profile.md` with this content:

```markdown
---
name: Developer Profile
description: Developer role, background, and collaboration preferences
type: user
---

## Role
[e.g., Backend Python developer with 3 years experience — filled by /init-project]

## Technical Background
- **Strong:** [languages/frameworks the developer knows well]
- **Learning:** [areas being developed]
- **Prefers to avoid:** [tech the developer wants to stay away from]

## How I Like to Collaborate
- [e.g., Explain WHY before HOW]
- [e.g., Show me the test before the implementation]
- [e.g., Keep responses concise — I can read diffs]

## Important Context
[Any other info agents should know about this developer]
```

- [ ] **Step 2.3: Create `project_decisions.md`**

Create `.claude/memory/project_decisions.md` with this content:

```markdown
---
name: Project Decisions
description: Key technical decisions made during development and the rationale behind them
type: project
---

## Decision Log

<!-- Agents: add entries here during /execute when significant decisions are made -->
<!-- Format: ## [Date] Decision Title -->
<!-- Then: **Decision:** what was decided -->
<!-- Then: **Why:** the reason -->
<!-- Then: **Trade-off:** what was given up -->

## Example Entry

### 2026-01-01 — Use SQLite over PostgreSQL for MVP
**Decision:** SQLite as the database for the first version.
**Why:** No infrastructure setup, zero configuration, sufficient for local demo.
**Trade-off:** Not suitable for production multi-user workloads. Migrate to PostgreSQL when needed.
```

- [ ] **Step 2.4: Create `feedback_corrections.md`**

Create `.claude/memory/feedback_corrections.md` with this content:

```markdown
---
name: Feedback and Corrections
description: Learnings from past sessions — what worked, what didn't, corrections to avoid repeating
type: feedback
---

## Corrections (Things to Avoid)

<!-- Add entries when something went wrong -->
<!-- Format: ### [Short title] -->
<!-- **Rule:** what not to do -->
<!-- **Why:** what went wrong -->
<!-- **How to apply:** when this matters -->

## Validated Approaches (Things to Keep Doing)

<!-- Add entries when an approach worked especially well -->
<!-- Format: ### [Short title] -->
<!-- **Rule:** what to keep doing -->
<!-- **Why:** why it worked -->

## Example Entry

### Don't generate the full implementation before tests
**Rule:** Always write the failing test first, run it to confirm it fails, then implement.
**Why:** Implementing first and adding tests after leads to tests that don't actually validate behavior.
**How to apply:** In /execute, if you're about to write implementation before tests — stop. Write the test first.
```

- [ ] **Step 2.5: Create `preferences/testing_style.md`**

Create `.claude/memory/preferences/testing_style.md` with this content:

```markdown
---
name: Testing Style
description: Test framework, TDD conventions, and fixture patterns for this project
type: user
---

## Framework
[Filled by /init-project — e.g., pytest with pytest-asyncio for async tests]

## Test Command
```bash
[e.g., pytest tests/ -v --tb=short]
```

## File Naming
- Test files: `tests/test_<module_name>.py`
- Test functions: `test_<what_it_does>_<condition>` (e.g., `test_create_task_with_missing_title_returns_422`)

## TDD Rules
1. Write the test FIRST — run it to confirm it fails before implementing
2. Write the MINIMUM implementation to make the test pass
3. Only refactor after tests are green
4. Never modify a test to make it pass — fix the implementation instead
5. Every new behavior needs a test; every bug fix needs a regression test

## What to Test
- Happy path: the expected successful case
- One failure case: what happens when input is wrong or missing
- Boundary: edge values (empty string, zero, None, max length)

## What NOT to Test
- Private implementation details (internals that could change)
- Framework behavior (e.g., don't test that FastAPI handles JSON parsing)
- Third-party library behavior

## Fixture Patterns
[Filled by /init-project — e.g., use pytest fixtures for database setup/teardown, not global state]
```

- [ ] **Step 2.6: Create `preferences/coding_style.md`**

Create `.claude/memory/preferences/coding_style.md` with this content:

```markdown
---
name: Coding Style
description: Naming conventions, file structure, and code patterns for this project
type: user
---

## Naming
- **Variables/functions:** [filled by /init-project — e.g., snake_case]
- **Classes:** [e.g., PascalCase]
- **Files:** [e.g., snake_case.py]
- **Constants:** [e.g., UPPER_SNAKE_CASE]

## File Structure
- One clear responsibility per file
- Group by feature/domain, not by type (e.g., `tasks/` contains models, routes, and tests for tasks — not `models/`, `routes/`, `tests/` as top-level folders)
- Keep files under ~200 lines — if a file grows beyond that, it's doing too much

## Error Handling
- Validate at system boundaries only (user input, external API responses)
- Don't add error handling for scenarios that can't happen
- Raise specific exceptions, not generic ones
- Return meaningful error messages to users (not raw exception messages)

## Code Patterns
- No magic numbers — use named constants
- No nested ternaries
- Functions do ONE thing — if you need "and" to describe it, split it
- No commented-out code in commits
- No TODO comments in commits — create a plan step instead

## What to Avoid
- Premature abstraction (don't extract a helper until you need it in 3+ places)
- Feature flags for features that can just be implemented
- Backwards compatibility shims for internal code
- Speculative "we might need this later" code
```

- [ ] **Step 2.7: Create `preferences/commit_style.md`**

Create `.claude/memory/preferences/commit_style.md` with this content:

```markdown
---
name: Commit Style
description: Git commit message format and tag conventions
type: user
---

## Format
[Filled by /init-project — conventional or simple]

## Conventional Format (if chosen)
```
<type>: <short description under 72 chars>

[Optional body: WHY this change was made, not what was changed]
```

Types:
- `feat:` — new feature or behavior
- `fix:` — bug fix
- `test:` — add or update tests
- `refactor:` — code change with no behavior change
- `docs:` — documentation only
- `chore:` — build/config/tooling changes

## Simple Format (if chosen)
```
add <thing>
fix <thing>
update <thing>
remove <thing>
```

## Rules
- Describe WHY, not what (the diff shows what; the message explains why)
- Under 72 characters for the first line
- Use imperative mood: "add feature" not "added feature" or "adds feature"
- One logical change per commit — don't bundle unrelated changes
- Never commit: secrets, .env files, generated build artifacts, commented-out code
```

- [ ] **Step 2.8: Verify all memory files exist**

```bash
find /Users/rohan.kantaria/Desktop/genAI/capstone_project/.claude/memory -type f | sort
```

Expected output (7 files):
```
.claude/memory/MEMORY.md
.claude/memory/feedback_corrections.md
.claude/memory/preferences/coding_style.md
.claude/memory/preferences/commit_style.md
.claude/memory/preferences/testing_style.md
.claude/memory/project_decisions.md
.claude/memory/user_profile.md
```

- [ ] **Step 2.9: Commit memory system**

```bash
git add .claude/memory/
git commit -m "feat: add structured memory system with MEMORY.md index and preference files"
```

---

## Task 3: `/help` Command

**Files:**
- Create: `.claude/commands/help.md`

**Acceptance criteria:** Running `/help` in Claude Code produces a formatted table of all 9 commands with descriptions and when to use each.

- [ ] **Step 3.1: Define acceptance criteria (read before writing)**

When a user runs `/help`, Claude Code will read `.claude/commands/help.md` and follow its instructions. The expected output is:

```
A markdown table with columns: Command | What it does | When to use it
All 9 commands listed: /help, /init-project, /prime, /create-prd, /plan-feature, /execute, /test, /commit, /review
```

- [ ] **Step 3.2: Write `.claude/commands/help.md`**

Create `.claude/commands/help.md` with this content:

```markdown
Display a formatted reference of all available slash commands for this project.

Output a markdown table with three columns: **Command**, **What it does**, **When to use it**

Include these commands:

| Command | What it does | When to use it |
|---|---|---|
| `/help` | Show this command reference | Whenever you forget a command or want an overview |
| `/init-project` | One-time setup after forking the template — fills CLAUDE.md and memory files | Once, immediately after forking |
| `/prime` | Load project context (CLAUDE.md, memory, PRD, latest plan) into a fresh session | At the start of every new Claude session |
| `/create-prd` | Interview mode — asks structured questions and writes a PRD to `.claude/PRD.md` | When starting a new app or adding a major feature |
| `/plan-feature [name]` | Analyzes codebase via parallel subagents and writes a step-by-step phase plan | After the PRD exists and you're ready to plan implementation |
| `/execute [plan-path]` | Runs the TDD cycle for each step: write tests → implement → lint → commit | When you're ready to implement a phase plan |
| `/test` | Runs the test suite, reports failures, attempts fixes, re-runs | For spot-checking outside of /execute |
| `/commit` | Creates a semantic git commit following the project's commit style | After any change you want to save but /execute didn't handle |
| `/review` | Reviews a completed phase: tests, lint, PRD alignment, structured pass/fail report | After /execute finishes a phase, before advancing to the next |

After the table, add a one-line tip:
> **Tip:** Start every session with `/prime` and end every phase with `/review`.
```

- [ ] **Step 3.3: Verify the file looks correct**

```bash
cat /Users/rohan.kantaria/Desktop/genAI/capstone_project/.claude/commands/help.md
```

Expected: file content matches what was written above. All 9 commands present.

- [ ] **Step 3.4: Commit**

```bash
git add .claude/commands/help.md
git commit -m "feat: add /help command"
```

---

## Task 4: `/prime` Command

**Files:**
- Create: `.claude/commands/prime.md`

**Acceptance criteria:** Running `/prime` outputs a structured context summary with project name, tech stack, PRD status, current phase, and next step — then asks what to work on.

- [ ] **Step 4.1: Write `.claude/commands/prime.md`**

Create `.claude/commands/prime.md` with this content:

```markdown
Load full project context for this session. Follow these steps in order:

**Step 1 — Read CLAUDE.md**
Read the CLAUDE.md file at the project root. Extract:
- Project name and description
- Tech stack (language, framework, database, test command, lint command)
- List of available commands

**Step 2 — Read memory index**
Read `.claude/memory/MEMORY.md`. Note which memory files exist and their purpose. Do NOT read the individual memory files yet — load them on demand when relevant to a task.

**Step 3 — Check PRD status**
Check if `.claude/PRD.md` exists.
- If yes: read it and summarize the MVP scope in 3-5 bullet points
- If no: note "PRD not yet created — run /create-prd to start"

**Step 4 — Check for active plan**
List files in `.agents/plans/`. Find the most recently modified file that is NOT `.gitkeep`.
- If found: read it and extract: phase name, number of completed steps (checked checkboxes), total steps, and the description of the FIRST unchecked step
- If none: note "No active plan — run /plan-feature [feature] to create one"

**Step 5 — Output context summary**

Format your output exactly like this:

---
## Session Context Loaded

**Project:** [project name] — [one-line description]

**Tech Stack:** [language] / [framework] / [database]
**Test command:** `[test command]`
**Lint command:** `[lint command]`

**PRD:** [✅ Exists — MVP covers: bullet 1, bullet 2, bullet 3] OR [❌ Not yet created]

**Active Plan:** [✅ phase-N-[name]: N/M steps done — Next: "[first unchecked step description]"] OR [❌ No active plan]

**Memory:** [N files available — load on demand via MEMORY.md index]

---

What would you like to work on?
```

- [ ] **Step 4.2: Verify file**

```bash
wc -l /Users/rohan.kantaria/Desktop/genAI/capstone_project/.claude/commands/prime.md
```

Expected: file exists and has content (should be 40+ lines).

- [ ] **Step 4.3: Commit**

```bash
git add .claude/commands/prime.md
git commit -m "feat: add /prime command for session context loading"
```

---

## Task 5: `/init-project` Command

**Files:**
- Create: `.claude/commands/init-project.md`

**Acceptance criteria:** Running `/init-project` asks 7 questions one at a time, then writes CLAUDE.md and all preference memory files with project-specific content.

- [ ] **Step 5.1: Write `.claude/commands/init-project.md`**

Create `.claude/commands/init-project.md` with this content:

```markdown
Run one-time project initialization for a freshly forked harness repo.

**Ask these questions ONE AT A TIME. Wait for the user's answer before asking the next question. Do not ask multiple questions at once.**

Ask in this order:
1. "What is the name of your project?"
2. "Describe the project in one sentence — what does it do?"
3. "What is your tech stack? List: programming language, framework (if any), database (if any)."
4. "What command runs your test suite? (e.g., `pytest tests/ -v`, `npm test`, `go test ./...`)"
5. "What command runs your linter/formatter? (e.g., `ruff check . --fix && ruff format .`, `npm run lint`, `golangci-lint run`). Type 'none' if you don't have one yet."
6. "What naming convention do you prefer for variables and functions? (snake_case / camelCase / other)"
7. "What git commit style do you prefer? (conventional: feat/fix/docs/test/refactor  OR  simple: add/fix/update)"

After collecting all answers, perform these writes:

**Write 1: Overwrite `CLAUDE.md`**

```markdown
# CLAUDE.md — [project name]

## Project
- **Name:** [project name]
- **Description:** [description]

## Commands

| Command | What it does |
|---|---|
| `/help` | List all commands |
| `/init-project` | One-time setup after forking |
| `/prime` | Load project context into a fresh session |
| `/create-prd` | Generate PRD through structured Q&A |
| `/plan-feature [name]` | Create phase implementation plan |
| `/execute [plan-path]` | TDD cycle: tests → implement → lint → commit |
| `/test` | Run tests, fix failures |
| `/commit` | Semantic git commit |
| `/review` | Phase review before advancing |

## Tech Stack
- **Language:** [language]
- **Framework:** [framework or "none"]
- **Database:** [database or "none"]
- **Test command:** `[test command]`
- **Lint command:** `[lint command or "none"]`

## Code Style
- **Naming:** [snake_case / camelCase / other]
- **Files:** Group by feature/domain, not by type
- **Functions:** One clear responsibility each — if you need "and" to describe it, split it

## Key Directories
- `.claude/commands/` — slash command definitions (do not modify unless improving the harness)
- `.claude/memory/` — persistent memory files
- `.agents/plans/` — generated phase implementation plans
- `.claude/PRD.md` — product requirements document
```

**Write 2: Overwrite `.claude/memory/user_profile.md`**

Fill in the `user_profile.md` template using the project name, tech stack, and any preferences the user mentioned.

**Write 3: Overwrite `.claude/memory/preferences/testing_style.md`**

Fill in the test framework section based on the test command provided:
- If `pytest` → note: use pytest fixtures, `tests/test_*.py` naming, `pytest tests/ -v --tb=short`
- If `npm test` or Jest → note: describe/it blocks, `__tests__/` directory
- If `go test` → note: `_test.go` suffix, table-driven tests

**Write 4: Overwrite `.claude/memory/preferences/coding_style.md`**

Fill in the naming convention based on the user's answer to question 6.

**Write 5: Overwrite `.claude/memory/preferences/commit_style.md`**

Fill in the commit format based on the user's answer to question 7:
- conventional → feat/fix/docs/test/refactor format with examples
- simple → add/fix/update format with examples

**Write 6: Update `.claude/memory/MEMORY.md`**

Ensure MEMORY.md index correctly reflects all memory files (it already does by default — verify it's intact).

After all writes are complete, output:

```
✅ Project initialized: [project name]

Files written:
- CLAUDE.md (project-specific)
- .claude/memory/user_profile.md
- .claude/memory/preferences/testing_style.md
- .claude/memory/preferences/coding_style.md
- .claude/memory/preferences/commit_style.md

Next steps:
1. Run `/prime` to verify context loads correctly
2. Run `/create-prd` to plan your app
3. Commit the initialized files: `git add -A && git commit -m "chore: initialize project with /init-project"`
```
```

- [ ] **Step 5.2: Verify file**

```bash
wc -l /Users/rohan.kantaria/Desktop/genAI/capstone_project/.claude/commands/init-project.md
```

Expected: 80+ lines.

- [ ] **Step 5.3: Commit**

```bash
git add .claude/commands/init-project.md
git commit -m "feat: add /init-project command for post-fork setup"
```

---

## Task 6: `/create-prd` Command

**Files:**
- Create: `.claude/commands/create-prd.md`
- Create: `.claude/PRD.md` (empty placeholder)

**Acceptance criteria:** Running `/create-prd` asks structured questions one at a time, then writes a complete PRD to `.claude/PRD.md` with all required sections.

- [ ] **Step 6.1: Create `.claude/PRD.md` placeholder**

Create `.claude/PRD.md` with this content:

```markdown
# Product Requirements Document

> This file is generated by running `/create-prd`.
> It will be overwritten with your project's PRD during that command.

Run `/create-prd` to generate your PRD.
```

- [ ] **Step 6.2: Write `.claude/commands/create-prd.md`**

Create `.claude/commands/create-prd.md` with this content:

```markdown
Generate a Product Requirements Document through structured interview.

**Before asking questions:**
Check if any non-markdown, non-config source files exist in the project root (e.g., .py, .ts, .js, .go files or src/ directories). If they do, use a subagent to briefly summarize the existing codebase structure — this prevents asking questions the code already answers.

**Ask these questions ONE AT A TIME. Wait for the answer before continuing.**

1. "What problem does this app solve? Who experiences this problem day-to-day?"

2. "Who are the target users? Be specific — not 'developers' but 'junior Python developers who are new to testing'."

3. "What are the 3-5 core features of the MVP? List them briefly — I'll ask follow-up questions about each."

4. For each feature the user listed, ask: "For [feature]: What's the simplest version of this that would still be useful? What does success look like?"

5. "What is explicitly OUT of scope for this first version? What are you deliberately not building yet?"

6. "How will you demo this? What does a successful 5-minute demo look like?"

After collecting all answers, write `.claude/PRD.md` with this exact structure:

---

```markdown
# Product Requirements Document
**Project:** [project name from CLAUDE.md]
**Created:** [today's date]

## Overview
**Problem:** [problem statement]
**Target Users:** [specific user description]
**Mission:** [one sentence: "A [what it is] that helps [who] to [do what]."]

## MVP Scope

### In Scope
[bullet list of features confirmed as in scope]

### Out of Scope (V1)
[bullet list of things explicitly excluded]

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
[etc.]

## Architecture Notes
**Tech Stack:** [from CLAUDE.md]
**Key Decisions:** [any decisions implied by the requirements, e.g., "SQLite sufficient for MVP — no multi-user concurrency needed"]
```

After writing the file, confirm:

```
✅ PRD written to .claude/PRD.md

Phases identified:
- Phase 1: [name]
- Phase 2: [name]
[etc.]

Next: Run `/plan-feature phase-1-[name]` to create the implementation plan for Phase 1.
```
```

- [ ] **Step 6.3: Verify both files exist**

```bash
ls -la /Users/rohan.kantaria/Desktop/genAI/capstone_project/.claude/
```

Expected: `PRD.md` and `commands/` both present.

- [ ] **Step 6.4: Commit**

```bash
git add .claude/commands/create-prd.md .claude/PRD.md
git commit -m "feat: add /create-prd command and PRD placeholder"
```

---

## Task 7: `/plan-feature` Command

**Files:**
- Create: `.claude/commands/plan-feature.md`

**Acceptance criteria:** Running `/plan-feature [name]` spawns 3 parallel subagents, synthesizes their findings, and writes a numbered phase plan with checkboxes to `.agents/plans/phase-N-[name].md`.

- [ ] **Step 7.1: Write `.claude/commands/plan-feature.md`**

Create `.claude/commands/plan-feature.md` with this content:

```markdown
Create a detailed implementation plan for a feature or phase.

The feature/phase name is in $ARGUMENTS (e.g., `/plan-feature phase-1-data-models`).

**Step 1 — Load context**
Read these files before doing anything:
- `CLAUDE.md` — tech stack, test command, lint command, naming conventions
- `.claude/PRD.md` — feature requirements and acceptance criteria
- `.claude/memory/preferences/testing_style.md` — how tests should be written

**Step 2 — Parallel codebase analysis**
Dispatch THREE subagents simultaneously. Give each subagent the project file list and its specific focus:

- **Subagent A (Structure):** List all existing source files, identify the file organization pattern, describe the naming conventions observed, note any existing utilities or helpers that could be reused
- **Subagent B (Dependencies):** Identify all imports and dependencies used, note any packages already installed (check package.json / requirements.txt / go.mod), identify which existing functions/classes could be called directly
- **Subagent C (Risks):** List 3-5 potential risks or edge cases for this feature, identify where validation is needed, note any security considerations (SQL injection, input validation, auth checks)

**Step 3 — Determine phase number**
Count the existing files in `.agents/plans/` (excluding `.gitkeep`). The new plan is `phase-[N+1]-[feature-name].md` where N is the current count.

**Step 4 — Write the implementation plan**

Write `.agents/plans/phase-[N]-[feature-name].md` with this structure:

```markdown
# Phase [N]: [Feature Name] — Implementation Plan

> **For agentic workers:** Run `/execute .agents/plans/[this-filename]` to implement this plan.

**Goal:** [one sentence describing what this phase delivers]
**Depends on:** [previous phases or "none"]
**Estimated steps:** [count]

---

## Step 1: [action name]

**What:** [brief description]
**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py`
- Test: `tests/test_file.py`

- [ ] Write the failing test:
```python
def test_[specific_behavior]():
    # [setup]
    result = [function call]
    assert result == [expected]
```

- [ ] Run test — confirm it fails:
```bash
[test command] tests/test_file.py::test_name -v
```
Expected: FAIL — "[function] not defined" or similar

- [ ] Implement:
```python
[actual implementation code]
```

- [ ] Run test — confirm it passes:
```bash
[test command] tests/test_file.py::test_name -v
```
Expected: PASS

- [ ] Run lint:
```bash
[lint command]
```
Expected: no errors

- [ ] Commit:
```bash
git add [files]
git commit -m "[type]: [description]"
```

[Repeat for each step]

---

## Acceptance Criteria Checklist
[Copy from PRD for this feature]
- [ ] [criterion 1]
- [ ] [criterion 2]
```

**Important rules for the plan:**
- Every step must contain ACTUAL CODE — no "implement the function here" placeholders
- Every test step must contain the actual test to write
- Every command step must show the exact command and expected output
- Steps should be small enough to complete in 2-5 minutes
- The plan must cover ALL acceptance criteria from the PRD for this feature

**Step 5 — Confirm**

Output:
```
✅ Plan written to .agents/plans/phase-[N]-[feature-name].md

Steps: [N] steps across [M] files
Covers acceptance criteria: [list them]

Run: /execute .agents/plans/phase-[N]-[feature-name].md
```
```

- [ ] **Step 7.2: Verify file**

```bash
wc -l /Users/rohan.kantaria/Desktop/genAI/capstone_project/.claude/commands/plan-feature.md
```

Expected: 80+ lines.

- [ ] **Step 7.3: Commit**

```bash
git add .claude/commands/plan-feature.md
git commit -m "feat: add /plan-feature command with parallel subagent analysis"
```

---

## Task 8: `/execute` Command

**Files:**
- Create: `.claude/commands/execute.md`

**Acceptance criteria:** Running `/execute [plan-path]` reads the plan, finds the first incomplete step, runs the full TDD cycle (write tests → implement → run tests → lint → commit), marks the step complete, and advances. Halts and reports if unresolvable after 3 attempts.

- [ ] **Step 8.1: Write `.claude/commands/execute.md`**

Create `.claude/commands/execute.md` with this content:

```markdown
Execute an implementation plan step-by-step using Test-Driven Development.

The plan file path is in $ARGUMENTS (e.g., `/execute .agents/plans/phase-1-setup.md`).

**Before starting:**
1. Read the plan file at the given path
2. Read `CLAUDE.md` — extract: test command, lint command
3. Read `.claude/memory/preferences/testing_style.md`
4. Read `.claude/memory/preferences/coding_style.md`
5. Identify the FIRST step with an unchecked checkbox (`- [ ]`)

If all steps are already checked: report "All steps complete. Run /review to validate the phase."

---

**For each incomplete step, determine its type and follow the matching procedure:**

### If the step is WRITE TESTS:
1. Write the test code exactly as shown in the plan
2. Run the test: `[test command from CLAUDE.md] [specific test file]::[test name] -v`
3. The test MUST fail at this point
   - If it passes: the feature already exists — report this and ask whether to skip or proceed
   - If it fails with the expected error: good — continue
4. Update the plan file: check the checkbox for this step (`- [ ]` → `- [x]`)

### If the step is IMPLEMENT:
1. Write the implementation code shown in the plan
2. Run the FULL test suite: `[test command from CLAUDE.md]`
3. If all tests pass: continue to next step
4. If tests fail:
   - Read each failure message carefully
   - Fix ONLY the implementation — NEVER modify a test to make it pass
   - Re-run the test suite
   - Repeat up to 3 attempts total
   - If still failing after 3 attempts: HALT. Output the failure messages and ask: "I've tried 3 times and the tests are still failing. Here's what's happening: [error]. How would you like to proceed?"
5. Update plan: check the checkbox

### If the step is RUN LINT:
1. Run: `[lint command from CLAUDE.md]`
2. If clean: continue
3. If errors:
   - Fix errors that can be auto-fixed (formatting, simple style issues)
   - Re-run lint
   - If errors remain: HALT and report which errors couldn't be fixed automatically
4. Update plan: check the checkbox

### If the step is COMMIT:
1. Read `.claude/memory/preferences/commit_style.md`
2. Stage the files listed in the plan step: `git add [files]`
3. Commit with the message from the plan (or generate one if the plan says "generate"):
   - conventional: `git commit -m "feat: [description]"`
   - simple: `git commit -m "add [description]"`
4. Verify: `git log --oneline -1`
5. Update plan: check the checkbox

---

**After each completed step:**
- Save the updated plan file with the checkbox marked
- Output: `✅ Step [N] complete: [step description]`
- Advance to the next incomplete step
- Repeat the procedure

**When all steps are complete:**
Output:
```
🎉 Phase complete! All [N] steps done.

Summary:
- Tests written: [N]
- Tests passing: [N]
- Commits made: [N]

Recommended next action: Run `/review .agents/plans/[filename]` to validate before advancing.
```

**General rules:**
- Never skip a step, even if it seems trivial
- Never mark a step complete until it fully passes
- Never modify tests to make them pass — fix the implementation
- If the plan has incomplete information for a step, ask the user before proceeding
- Keep the plan file updated in real-time (check boxes as you go)
```

- [ ] **Step 8.2: Verify file**

```bash
wc -l /Users/rohan.kantaria/Desktop/genAI/capstone_project/.claude/commands/execute.md
```

Expected: 90+ lines.

- [ ] **Step 8.3: Commit**

```bash
git add .claude/commands/execute.md
git commit -m "feat: add /execute command with full TDD cycle pipeline"
```

---

## Task 9: `/test`, `/commit`, and `/review` Commands

**Files:**
- Create: `.claude/commands/test.md`
- Create: `.claude/commands/commit.md`
- Create: `.claude/commands/review.md`

**Acceptance criteria:** All three commands produce structured, actionable output. `/test` does not commit. `/commit` reads commit style from memory. `/review` produces a clear APPROVED or BLOCKED verdict.

- [ ] **Step 9.1: Write `.claude/commands/test.md`**

Create `.claude/commands/test.md` with this content:

```markdown
Run the project test suite, report results, fix failures if possible, and re-run.

1. Read `CLAUDE.md` to get the test command (look for the "Test command:" line)
2. Run the test command
3. Parse the output:
   - If all tests pass: output "✅ All tests passing. [N] tests in [X]s." and stop
   - If tests fail: continue to step 4
4. For each failing test:
   - Read the test code to understand what behavior is expected
   - Read the implementation file being tested
   - Determine if the failure is a bug in the implementation or a bug in the test itself
   - If implementation bug: fix the implementation only
   - If test bug (test is genuinely wrong, not just failing): report it and ask before changing
5. Re-run the test suite
6. Repeat up to 3 total attempts
7. If tests still failing after 3 attempts:
   Output:
   ```
   ❌ Tests still failing after 3 attempts.

   Failing tests:
   - [test name]: [error message]
   [etc.]

   I did not commit any changes. Please review the failures above.
   ```
8. Do NOT commit — /test is for checking and diagnosis only
```

- [ ] **Step 9.2: Write `.claude/commands/commit.md`**

Create `.claude/commands/commit.md` with this content:

```markdown
Create a semantic git commit for the current changes.

1. Run `git status` — identify modified, new, and deleted files
2. Run `git diff` and `git diff --staged` — read what actually changed
3. Read `.claude/memory/preferences/commit_style.md` — get the commit format (conventional or simple)
4. Generate a commit message:
   - Describe WHY this change was made, not just what changed
   - Use the project's chosen format (conventional or simple)
   - Keep the first line under 72 characters
   - Use imperative mood: "add auth middleware" not "added auth middleware"
5. Stage the relevant files (do NOT stage unrelated files, .env, or secrets):
   `git add [specific files]`
6. Create the commit:
   `git commit -m "[generated message]"`
7. Verify: `git log --oneline -1`
8. Output: `✅ Committed: [hash] [message]`

If there is nothing to commit (clean working tree), output: "Nothing to commit — working tree is clean."
If there are untracked files that look like they should be committed, ask before staging them.
```

- [ ] **Step 9.3: Write `.claude/commands/review.md`**

Create `.claude/commands/review.md` with this content:

```markdown
Review a completed implementation phase before marking it done and advancing.

If $ARGUMENTS contains a path, use that plan file. Otherwise, find the most recently modified file in `.agents/plans/` (excluding `.gitkeep`).

**Step 1 — Run tests**
Run the test command from CLAUDE.md.
Record: pass count, fail count, any errors.

**Step 2 — Run lint**
Run the lint command from CLAUDE.md.
Record: clean or N errors.

**Step 3 — PRD alignment**
Read `.claude/PRD.md`. Find the acceptance criteria for the phase being reviewed.
For each criterion, check: is there code that implements it? Is there a test that verifies it?
Mark each as: ✅ covered / ⚠️ partially covered / ❌ missing

**Step 4 — Coverage gaps**
For each feature in the phase, check:
- Is there a test for the happy path?
- Is there at least one failure/edge case test?
If either is missing, note it.

**Step 5 — Quick code scan**
Look at the files changed in this phase. Flag if any of these are present:
- Hardcoded values that should be constants or config
- Missing input validation at user-facing entry points (routes, CLI args)
- Functions longer than ~30 lines (may be doing too much)
- Obvious security issues (SQL concatenation instead of parameterized queries, etc.)

**Step 6 — Output structured report**

```
## Phase Review: [phase name]

**Tests:** ✅ [N] passing / ❌ [N] failing
**Lint:** ✅ Clean / ❌ [N] errors

**PRD Alignment:**
- [criterion 1]: ✅ covered
- [criterion 2]: ⚠️ partially covered — [what's missing]
- [criterion 3]: ❌ not implemented

**Coverage Gaps:**
- [feature]: missing failure case test

**Code Issues:**
- [file:line]: [specific issue]

---
**Verdict:** ✅ APPROVED — safe to advance to next phase
         OR ❌ BLOCKED — fix these before advancing:
            1. [specific item]
            2. [specific item]
```

If APPROVED, suggest the next phase plan to create: "Run `/plan-feature phase-[N+1]-[name]` to continue."
```

- [ ] **Step 9.4: Verify all three files exist**

```bash
ls /Users/rohan.kantaria/Desktop/genAI/capstone_project/.claude/commands/
```

Expected: all 9 command files present: `commit.md  create-prd.md  execute.md  help.md  init-project.md  plan-feature.md  prime.md  review.md  test.md`

- [ ] **Step 9.5: Commit**

```bash
git add .claude/commands/test.md .claude/commands/commit.md .claude/commands/review.md
git commit -m "feat: add /test, /commit, and /review commands"
```

---

## Task 10: `README.md`

**Files:**
- Create: `README.md`

**Acceptance criteria:** README explains what the harness is, how to fork it, lists all commands, and walks through the demo narrative.

- [ ] **Step 10.1: Write `README.md`**

Create `README.md` with this content:

```markdown
# AI Development Harness

A reusable Claude Code template that automates the full development lifecycle:
**PRD → Phase Plans → TDD Implementation → Review → Commit**

Built as a capstone project for the Slalom Software Engineering with AI bootcamp. Demonstrates: AI agents, persistent memory, slash commands, context engineering, and Test-Driven Development.

## What's Inside

```
├── CLAUDE.md                    # Project context (filled by /init-project)
├── .claude/
│   ├── PRD.md                   # Product Requirements (generated by /create-prd)
│   ├── commands/                # 9 slash commands
│   └── memory/                  # Persistent memory system
│       ├── MEMORY.md            # Memory index
│       ├── user_profile.md
│       ├── project_decisions.md
│       ├── feedback_corrections.md
│       └── preferences/         # Test style, coding style, commit style
└── .agents/plans/               # Generated phase plans
```

## How to Use

### Fork and Set Up
1. Fork this repo
2. Clone your fork
3. Open in Claude Code
4. Run `/init-project` — answers 7 questions, fills in CLAUDE.md and memory files
5. Run `/prime` — verify context loads correctly

### Plan Your App
6. Run `/create-prd` — structured Q&A generates a PRD
7. Run `/plan-feature phase-1-[name]` — creates a step-by-step implementation plan

### Build with TDD
8. Run `/execute .agents/plans/phase-1-[name].md` — implements each step with TDD
9. Run `/review` — validates phase before advancing
10. Repeat steps 7-9 for each phase

### Other Commands
- `/help` — list all commands
- `/test` — run tests and fix failures (standalone)
- `/commit` — semantic git commit

## Memory System

Memory files persist across Claude sessions. They are NOT auto-loaded (keeps context clean).
Use `/prime` at the start of each session to load the index and relevant context.

| File | What it stores |
|---|---|
| `user_profile.md` | Your role, tech background, collaboration preferences |
| `project_decisions.md` | Key decisions made + rationale (written by agents during /execute) |
| `feedback_corrections.md` | Session learnings — what worked, what didn't |
| `preferences/testing_style.md` | Test framework, TDD conventions, fixture patterns |
| `preferences/coding_style.md` | Naming, file structure, error handling patterns |
| `preferences/commit_style.md` | Commit message format preferences |

## The `/execute` TDD Pipeline

Each step in a phase plan goes through this cycle automatically:

```
1. Write failing test (red)
2. Run test → confirm it fails
3. Implement feature (green)
4. Run full test suite → fix if failing (max 3 attempts)
5. Run linter → fix errors
6. Git commit with semantic message
7. Mark step complete → advance to next
8. Halt and report if unresolvable
```

## Demo Narrative (5-10 min)

| Time | What to show |
|---|---|
| 0-1 min | This README + directory structure |
| 1-2 min | Memory files — show user_profile.md and preferences/ |
| 2-3 min | Run `/prime` — context loaded from memory |
| 3-5 min | Run `/create-prd` — live Q&A generates a PRD |
| 5-7 min | Run `/plan-feature` — subagents analyze, plan written |
| 7-9 min | Run `/execute phase-1` — TDD cycle runs: test → implement → pass → commit |
| 9-10 min | Run `/review` — structured pass/fail report |

## Bootcamp Concepts Demonstrated

| Concept | Where it appears |
|---|---|
| AI Agents | Subagents in `/plan-feature`, `/create-prd`, `/review` |
| Memory Management | `.claude/memory/` persists across sessions; `/prime` loads on demand |
| Context Engineering | CLAUDE.md + PRD + phase plans focus agents without polluting context |
| MCP Servers | Filesystem MCP used by agents to read/write files |
| Slash Commands | 9 custom commands automate the dev lifecycle |
| Test-Driven Development | `/execute` enforces red→green→refactor per step |

---

Built by Rohan Kantaria — Slalom Capstone, 2026
```

- [ ] **Step 10.2: Verify README**

```bash
wc -l /Users/rohan.kantaria/Desktop/genAI/capstone_project/README.md
```

Expected: 100+ lines.

- [ ] **Step 10.3: Commit**

```bash
git add README.md
git commit -m "docs: add README with setup instructions, command reference, and demo narrative"
```

---

## Task 11: Move Spec and Plan Files Into Repo + Final Commit

**Files:**
- Confirm: `docs/superpowers/specs/2026-04-14-ai-dev-harness-design.md` (already exists)
- Confirm: `docs/superpowers/plans/2026-04-14-ai-dev-harness.md` (this file)

- [ ] **Step 11.1: Verify all files are committed**

```bash
git status
```

Expected: clean working tree (or only docs/superpowers/ files unstaged).

- [ ] **Step 11.2: Stage and commit docs**

```bash
git add docs/
git commit -m "docs: add design spec and implementation plan"
```

- [ ] **Step 11.3: Verify full file list**

```bash
find /Users/rohan.kantaria/Desktop/genAI/capstone_project -not -path '*/.git/*' -type f | sort
```

Expected output (22 files):
```
./CLAUDE.md
./README.md
./.gitignore
./.agents/plans/.gitkeep
./.claude/PRD.md
./.claude/commands/commit.md
./.claude/commands/create-prd.md
./.claude/commands/execute.md
./.claude/commands/help.md
./.claude/commands/init-project.md
./.claude/commands/plan-feature.md
./.claude/commands/prime.md
./.claude/commands/review.md
./.claude/commands/test.md
./.claude/memory/MEMORY.md
./.claude/memory/feedback_corrections.md
./.claude/memory/preferences/coding_style.md
./.claude/memory/preferences/commit_style.md
./.claude/memory/preferences/testing_style.md
./.claude/memory/project_decisions.md
./.claude/memory/user_profile.md
./docs/architecture.md
./docs/superpowers/plans/2026-04-14-ai-dev-harness.md
./docs/superpowers/specs/2026-04-14-ai-dev-harness-design.md
```

- [ ] **Step 11.4: Verify git log**

```bash
git log --oneline
```

Expected: 11 commits visible, in order:
```
[hash] docs: add design spec and implementation plan
[hash] docs: add README with setup instructions...
[hash] feat: add /test, /commit, and /review commands
[hash] feat: add /execute command with full TDD cycle pipeline
[hash] feat: add /plan-feature command with parallel subagent analysis
[hash] feat: add /create-prd command and PRD placeholder
[hash] feat: add /init-project command for post-fork setup
[hash] feat: add /prime command for session context loading
[hash] feat: add /help command
[hash] feat: add structured memory system...
[hash] chore: initialize harness project structure
```

---

## Task 12: End-to-End Verification

**Goal:** Run through the full harness workflow from scratch to confirm everything works together.

- [ ] **Step 12.1: Test `/help`**

In Claude Code, run:
```
/help
```
Expected: A table with all 9 commands listed with descriptions and when-to-use column.
If the table is missing any commands or has formatting issues, fix `help.md`.

- [ ] **Step 12.2: Test `/prime` (before initialization)**

In Claude Code, run:
```
/prime
```
Expected: Summary showing placeholder content from CLAUDE.md, note that PRD doesn't exist yet, no active plan.
The output should be structured and clearly indicate what's set up vs. not.

- [ ] **Step 12.3: Test `/init-project`**

In Claude Code, run:
```
/init-project
```
Answer with a test project:
- Name: "Test App"
- Description: "A simple REST API for managing todo items"
- Stack: Python / FastAPI / SQLite
- Test command: `pytest tests/ -v`
- Lint command: `ruff check . --fix && ruff format .`
- Naming: snake_case
- Commit style: conventional

Expected: CLAUDE.md is rewritten with project-specific content. All 5 preference memory files are updated. Confirmation message with next steps.

Verify CLAUDE.md was updated:
```bash
head -20 /Users/rohan.kantaria/Desktop/genAI/capstone_project/CLAUDE.md
```
Expected: Shows "Test App" as project name, pytest and ruff as commands.

- [ ] **Step 12.4: Test `/prime` (after initialization)**

In Claude Code, run:
```
/prime
```
Expected: Now shows "Test App" as project name, correct tech stack, pytest command, ruff lint command. PRD still noted as not created.

- [ ] **Step 12.5: Test `/create-prd`**

In Claude Code, run:
```
/create-prd
```
Answer the questions with simple answers for a todo app.

Expected: `.claude/PRD.md` is written with all required sections (Overview, MVP Scope, Core Features with acceptance criteria, Success Criteria, Implementation Phases, Architecture Notes).

Verify:
```bash
wc -l /Users/rohan.kantaria/Desktop/genAI/capstone_project/.claude/PRD.md
```
Expected: 50+ lines.

- [ ] **Step 12.6: Revert test initialization changes**

Since steps 12.3-12.5 modified CLAUDE.md and PRD.md for a "Test App", revert to template state:

```bash
git checkout CLAUDE.md .claude/PRD.md .claude/memory/
```

Verify the files are back to template content:
```bash
head -5 /Users/rohan.kantaria/Desktop/genAI/capstone_project/CLAUDE.md
```
Expected: Shows the placeholder text "run /init-project to fill this in".

- [ ] **Step 12.7: Final git status check**

```bash
git status
```
Expected: clean working tree.

---

## Self-Review Checklist

After completing all tasks:

- [ ] All 9 commands exist in `.claude/commands/`
- [ ] All 7 memory files exist in `.claude/memory/`
- [ ] Every command file has actual content (no TBD/TODO placeholders)
- [ ] `/execute` command clearly specifies the TDD cycle (tests first, implement, lint, commit)
- [ ] `/review` outputs a clear APPROVED/BLOCKED verdict
- [ ] `README.md` covers: what it is, how to fork it, command reference, demo narrative, bootcamp concepts
- [ ] `git log` shows clean, semantic commits
- [ ] All files committed to git
