Generate a Product Requirements Document through structured interview.

**Before asking questions:**
Check if any non-markdown, non-config source files exist in the project (e.g., .py, .ts, .js, .go files or src/ directories). If they do, use a subagent to briefly summarize the existing codebase structure — this prevents asking questions the code already answers.

**Ask these questions ONE AT A TIME. Wait for the answer before continuing.**

1. "What problem does this app solve? Who experiences this problem day-to-day?"

2. "Who are the target users? Be specific — not 'developers' but 'junior Python developers who are new to testing'."

3. "What are the 3-5 core features of the MVP? List them briefly — I'll ask follow-up questions about each."

4. For each feature the user listed in Q3: if they did NOT already describe the simplest version or success criteria for that feature, ask: "For [feature name]: What's the simplest version of this that would still be useful? What does success look like?" — Skip this question for any feature where Q3 already provided this detail.

5. "What is explicitly OUT of scope for this first version? What are you deliberately not building yet?"

6. "How will you demo this? What does a successful 5-minute demo look like?"

After collecting all answers, write `.claude/PRD.md` with this exact structure:

```markdown
# Product Requirements Document
**Project:** [project name from CLAUDE.md]
**Created:** [today's date]

## Overview
**Problem:** [problem statement]
**Target Users:** [specific user description]
**Mission:** [one sentence: "A [what it is] that helps [who] to [do what]."]

## MVP Scope

### In Scope
- [feature 1]
- [feature 2]
- [feature 3]

### Out of Scope (V1)
- [explicitly excluded item 1]
- [explicitly excluded item 2]

## Core Features

### Feature 1: [name]
**Description:** [what it does]
**User Story:** As a [user], I want to [action] so that [outcome].
**Acceptance Criteria:**
- [ ] [specific, testable criterion]
- [ ] [specific, testable criterion]

[Repeat for each feature]

## Success Criteria
- [ ] [measurable outcome that proves the MVP works]
- [ ] [another measurable outcome]

## Implementation Phases
[Suggest 2-4 phases based on the features, ordered by dependency]

**Phase 1 — [name]:** [what this phase delivers]
**Phase 2 — [name]:** [what this phase delivers]

## Architecture Notes
**Tech Stack:** [from CLAUDE.md]
**Key Decisions:** [any decisions implied by the requirements]
```

After writing the file, output:

```
✅ PRD written to .claude/PRD.md

Phases identified:
- Phase 1: [name]
- Phase 2: [name]

Next: Run `/plan-feature phase-1-[name]` to create the implementation plan for Phase 1.
```
