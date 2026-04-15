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
- Use imperative mood: "add feature" not "added feature"
- One logical change per commit — don't bundle unrelated changes
- Never commit: secrets, .env files, generated build artifacts, commented-out code
