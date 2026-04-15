Create a semantic git commit for the current changes.

If $ARGUMENTS contains a message (e.g., `/commit "feat: add task model"`), use that message directly. Otherwise generate one.

**Step 1 — Check for changes**
Run `git status`.
- If the working tree is clean: output "Nothing to commit — working tree is clean." and stop.
- If there are changes: continue.

**Step 2 — Understand what changed**
Run `git diff` and `git diff --staged` to read what actually changed.

**Step 3 — Read commit style**
Read `.claude/memory/preferences/commit_style.md` to get the commit format (conventional or simple).

**Step 4 — Generate commit message (if not provided in $ARGUMENTS)**
- Describe WHY this change was made, not just what changed
- Use the project's chosen format (conventional or simple)
- Keep the first line under 72 characters
- Use imperative mood: "add auth middleware" not "added auth middleware"

**Step 5 — Stage files**
Stage all modified files that are part of a logical unit:
```bash
git add [specific files]
```
Do NOT stage: .env files, secrets, unrelated changes, or binary build artifacts.

If there are untracked files that look like they should be committed, list them and ask before staging.

**Step 6 — Commit**
```bash
git commit -m "[message]"
```

**Step 7 — Verify**
Run `git log --oneline -1`.
Output: `✅ Committed: [hash] — [message]`
