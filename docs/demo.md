# Demo Guide

A walkthrough for demoing the AI Dev Harness end-to-end in 5–10 minutes.

## What to Show

| Time | What to do |
|---|---|
| 0–1 min | Walk through the repo structure — point out `.claude/commands/`, `.claude/memory/`, `.agents/plans/` |
| 1–3 min | Run `/create-prd` — live Q&A defines the app, writes PRD + CLAUDE.md + memory preference files |
| 3–4 min | Open `.claude/PRD.md` and `.claude/memory/preferences/` — show what was generated |
| 4–5 min | Run `/prime` — show context loading from generated files |
| 5–7 min | Run `/plan-feature` — parallel subagents analyze the codebase, plan files written for all phases |
| 7–9 min | Run `/execute .agents/plans/phase-1-[name].md` — TDD cycle live: test written → fails → implemented → passes → committed |
| 9–10 min | Run `/review` — structured pass/fail report against PRD criteria |

## Key Talking Points

**Slash commands as agent instructions**
Each command in `.claude/commands/` is a markdown file that Claude Code reads as a structured prompt. Commands chain together — `/create-prd` output feeds `/plan-feature`, which feeds `/execute`.

**Memory persistence**
`.claude/memory/` files survive across sessions. `/prime` loads them on demand rather than auto-loading everything — keeps the context window lean on long-running projects.

**Parallel subagents in `/plan-feature`**
Three subagents run simultaneously: one maps file structure, one audits dependencies, one identifies risks. Results are synthesized into the plan before any code is written.

**TDD enforcement in `/execute`**
The command refuses to advance past a test step if the test passes before implementation exists. It also refuses to modify tests to make them pass — only the implementation can change.

**Hard verdict in `/review`**
APPROVED or BLOCKED — no ambiguous "looks mostly good." BLOCKED means a specific list of things that need fixing before you can advance to the next phase.

## Concepts Illustrated

| Concept | Where it appears |
|---|---|
| AI Agents | Parallel subagents in `/plan-feature`; codebase scan subagent in `/create-prd` |
| Persistent Memory | `.claude/memory/` files read by `/prime` at session start |
| Context Engineering | CLAUDE.md + PRD + phase plans scope each agent precisely |
| Slash Commands | 8 custom commands automate the full development lifecycle |
| Test-Driven Development | `/execute` enforces write test → implement → pass → lint → commit |
