<!-- Maintainer: if you add a new command to .claude/commands/, add a row to the table below too -->
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
| `/commit` | Creates a semantic git commit following the project's commit style | After any change you want to save that /execute didn't handle |
| `/review` | Reviews a completed phase: tests, lint, PRD alignment, structured pass/fail report | After /execute finishes a phase, before advancing to the next |

After the table, add:
> **Tip:** Start every session with `/prime` and end every phase with `/review`.
