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

Ask yourself: what problem does this change solve? What would break or be missing without it? Use the answer to write the subject line — not a description of the diff.

Rules:
- Use the project's commit format (conventional or simple) from commit_style.md
- Single line only — keep under 72 characters (multi-line messages require a different approach not covered here)
- Use imperative mood: "add auth middleware" not "added auth middleware" or "adds auth middleware"
- Subject line describes WHY, not WHAT — the diff already shows what changed

**Step 5 — Security scan**
Before staging anything, scan all modified and untracked files for secrets:
- Patterns to flag: `sk-`, `Bearer `, `api_key =`, `password =`, `secret =`, `token =`, any environment variable with a real value assigned inline
- Check that `.env` is in `.gitignore` — if not, add it now
- If a secret is found: HALT. Output:
  ```
  🚨 Secret detected — not staging.

  Found: [what, in which file, on which line]

  Fix: move the value to .env and load it via os.environ or python-dotenv.
  ```

**Step 6 — Stage files**
Only stage files that belong to the same logical change:
```bash
git add [specific files]
```
Do NOT stage: `.env` files, secrets, unrelated changes, or binary build artifacts.

If there are untracked files that look relevant, list them and ask the developer before staging.

**Step 7 — Commit**
```bash
git commit -m "[message]"
```

**Step 8 — Verify**
Run `git log --oneline -1`.
Output: `✅ Committed: [hash] — [message]`
