# File templates and conventions

Use these as starting structures. Keep entries short and skimmable — these files are meant to be re-read quickly at the start of every session, not archives of everything that was ever said.

Dates: use `YYYY-MM-DD` throughout.

---

## PROJECT.md

The stable overview. Changes rarely — only on real structural shifts.

```markdown
# Project

## What this is
<1-3 sentences: what the project is and who/what it's for>

## Tech stack
- <language/framework/runtime>
- <database/infra/hosting>
- <notable libraries>

## Architecture / structure
<brief description or file/folder layout of how the codebase is organized>

## Conventions
- <coding style, naming, testing conventions, anything Claude should follow consistently>
```

---

## REQUIREMENTS.md

```markdown
# Requirements

## Functional requirements
- <what the system must do, one line each>

## Non-functional requirements
- <performance, security, scale, compliance constraints, etc.>

## Acceptance criteria
- <how you know a given requirement is actually met>

## Open / unresolved requirements
- <things still being decided>
```

---

## ROADMAP.md

```markdown
# Roadmap

## Phases
- [ ] Phase 1: <name> — <one-line description>
- [ ] Phase 2: <name> — <one-line description>

## Key decisions
- **YYYY-MM-DD** — <decision> — why: <reason>. (Supersedes: <earlier decision>, if applicable)

## Deferred / explicitly out of scope
- <idea> — deferred because <reason>
```

---

## STATE.md

This file is **overwritten**, not appended to. It should always reflect the current moment.

```markdown
# Current State
_Last updated: YYYY-MM-DD_

## In progress
- <task> — <brief state, e.g. "backend done, wiring up frontend">

## Blockers
- <blocker> — <what's needed to unblock>

## Next steps
1. <next concrete action>
2. <next concrete action>
```

---

## COMPLETED.md

Append-only. Newest entry at the top. Never delete old entries.

```markdown
# Completed Work

## YYYY-MM-DD
- <what was finished, one line>
- <what was finished, one line>

## YYYY-MM-DD
- ...
```

---

## convo-N.md

One per work session (or an extra mid-session one if the 90%-context-usage trigger fires). Determine N by counting existing `convo-*.md` files in the folder and using the next integer (first session is `convo-1.md`). If a 90% safety dump already created `convo-N.md` earlier in the session, don't create another for the same session at the end — update/replace that one instead.

**Content = the actual conversation, as fully as you can reproduce it — not a condensed summary.** Use a short header, then the transcript:

```markdown
# Session N — YYYY-MM-DD
_Trigger: [end of session | 90% context usage safety dump]_

## Transcript
<the conversation content itself: what the user asked, what was discussed,
decisions made, code/commands run, and Claude's responses, in order,
as completely as possible>

## Open questions / carried forward
- <anything unresolved to pick up next time>
```

The header and "Open questions" section keep it skimmable for a future session; the transcript body is the actual record, not a synopsis. If a session was purely exploratory with no concrete output, the transcript can still be short — but it should still be what was actually said, not a one-line gloss.
