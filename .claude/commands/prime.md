Load full project context for this session. Follow these steps in order:

**Step 1 — Read CLAUDE.md**
Read the CLAUDE.md file at the project root. Extract:
- Project name and description
- Tech stack (language, framework, database, test command, lint command)
- List of available commands

If the project name field still contains placeholder text (e.g., "[run /init-project to fill this in]"), stop immediately and output: "⚠️ CLAUDE.md has not been initialized yet. Run /init-project first, then /prime."

**Step 2 — Read memory index**
Read `.claude/memory/MEMORY.md`. Note which memory files exist and their purpose. Do NOT read the individual memory files yet — load them on demand when relevant to a task.

**Step 3 — Check PRD status**
Check if `.claude/PRD.md` exists.
- If yes: read it and summarize the MVP scope in 3-5 bullet points
- If no: note "PRD not yet created — run /create-prd to start"

**Step 4 — Check for active plan**
Check if `.agents/plans/` exists. If the directory does not exist, note "No active plan — run /plan-feature [name]" and skip the rest of Step 4. If it exists, list its files. Find the most recently modified file that is NOT `.gitkeep`.
- If found: read it and extract: phase name, number of completed steps (checked checkboxes `- [x]`), total steps, and the description of the FIRST unchecked step (`- [ ]`)
- If none: note "No active plan — run /plan-feature [feature] to create one"

**Step 5 — Output context summary**

Format your output exactly like this:

---
## Session Context Loaded

**Project:** [project name] — [one-line description]

**Tech Stack:** [language] / [framework] / [database]
**Test command:** `[test command]`
**Lint command:** `[lint command]`

**PRD:** [✅ Exists — MVP: bullet 1, bullet 2, bullet 3] OR [❌ Not yet created — run /create-prd]

**Active Plan:** [✅ phase-N-[name]: N/M steps done — Next: "[first unchecked step description]"] OR [❌ No active plan — run /plan-feature [name]]

**Memory:** [N files available — load on demand via MEMORY.md index]

---

What would you like to work on?
