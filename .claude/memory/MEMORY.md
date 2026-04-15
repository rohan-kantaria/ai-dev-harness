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

## When to Update Each File

| File | Updated by | When |
|---|---|---|
| `user_profile.md` | Developer (manually) | When your preferences or background change |
| `project_decisions.md` | Agent during `/execute` | When a significant technical decision is made |
| `feedback_corrections.md` | Developer (manually) | After each session ends |
| `preferences/*.md` | `/init-project`, then developer | On fork setup, then as preferences evolve |

## Memory Types

The `type` field in each file's frontmatter categorizes the memory:
- `user` — developer preferences, background, how they work
- `project` — decisions and context specific to this codebase
- `feedback` — corrections and validated approaches from past sessions
- `reference` — pointers to external resources (e.g., API docs, dashboards)
