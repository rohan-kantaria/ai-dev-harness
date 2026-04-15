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
