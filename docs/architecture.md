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

```text
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
