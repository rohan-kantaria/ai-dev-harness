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
| `/commands` | List all commands |
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

Fill ONLY these two sections using the Q&A answers, keep all other sections as their template placeholders:
- **Role:** derive from tech stack answer (e.g., "Python / FastAPI developer")
- **Technical Background → Strong:** list the tech stack components named

Leave "How I Like to Collaborate" and "Important Context" as their existing template placeholders — the developer fills these manually after setup. Keep the frontmatter fields (name, description, type: user) intact.

**Write 3: Overwrite `.claude/memory/preferences/testing_style.md`**

Fill in the test framework section based on the test command provided:
- If `pytest` → note: use pytest fixtures, `tests/test_*.py` naming, run with `pytest tests/ -v --tb=short`
- If the command contains `jest` → note: Jest describe/it blocks, `__tests__/` or `*.test.js` files, run with the provided command
- If the command contains `vitest` → note: Vitest describe/it blocks, similar to Jest API, run with the provided command
- If the command is `npm test` without a specific runner named → ask: "Which test runner does `npm test` invoke? (Jest / Vitest / Mocha)" before writing conventions
- If the command contains `mocha` → note: Mocha describe/it blocks, `test/` directory
- If `go test` → note: `_test.go` suffix, table-driven tests, run with `go test ./... -v`
- Otherwise → adapt based on the command provided

Keep the TDD Rules section unchanged (it's universal).

**Write 4: Overwrite `.claude/memory/preferences/coding_style.md`**

Fill in the Naming section with the convention from question 6. Add concrete examples matching the chosen convention. Keep all other sections intact.

**Write 5: Overwrite `.claude/memory/preferences/commit_style.md`**

Fill in the Format section based on question 7:
- conventional → mark "Conventional Format" as the active choice, add 2-3 concrete examples using feat/fix/docs
- simple → mark "Simple Format" as the active choice, add 2-3 concrete examples

**Write 6: Update `.claude/memory/MEMORY.md`**

Verify the MEMORY.md index is intact (it already should be). Do not change it unless a file reference is broken.

After all writes, output:

```
✅ Project initialized: [project name]

Files written:
- CLAUDE.md
- .claude/memory/user_profile.md
- .claude/memory/preferences/testing_style.md
- .claude/memory/preferences/coding_style.md
- .claude/memory/preferences/commit_style.md

Next steps:
1. Run `/prime` to verify context loads correctly
2. Run `/create-prd` to plan your app
3. Commit the initialized files: `git add -A && git commit -m "chore: initialize project with /init-project"`
```
