<!-- Maintainer: if you add a new command to .claude/commands/, add a row to the table below too -->
Display a formatted reference of all available slash commands for this project.

Output a markdown table with three columns: **Command**, **What it does**, **When to use it**

Include these commands:

| Command | What it does | When to use it |
|---|---|---|
| `/commands` | Show this command reference | Whenever you forget a command or want an overview |
| `/prime` | Load project context (CLAUDE.md, memory, PRD, latest plan) into a fresh session | At the start of every new Claude session |
| `/create-prd` | Interview mode — defines requirements AND configures CLAUDE.md + memory files | First thing after forking — replaces manual setup |
| `/plan-feature [name]` | No args: plans all PRD phases. With name: plans one specific phase | After the PRD is approved |
| `/execute [plan-path]` | Runs the TDD cycle for each step: write tests → implement → lint → commit | When you're ready to implement a phase plan |
| `/test` | Runs the test suite, reports failures, attempts fixes, re-runs | For spot-checking outside of /execute |
| `/commit` | Creates a semantic git commit following the project's commit style | After any change you want to save that /execute didn't handle |
| `/review` | Reviews a completed phase: tests, lint, PRD alignment, structured pass/fail report | After /execute finishes a phase, before advancing to the next |
| `/init-project` | Manual project setup without a PRD (fills CLAUDE.md and memory files) | Only if you need to configure the harness without running /create-prd |

After the table, add:
> **Tip:** The normal flow is `/create-prd` → `/plan-feature` → `/execute` → `/review`. Start every session with `/prime`.
