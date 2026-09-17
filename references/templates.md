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

## Implemented but not yet verified
- <thing that was written/changed but hasn't been tested/run/confirmed> — <what verification is still needed>

## Blockers
- <blocker> — <what's needed to unblock>

## Next steps
1. <next concrete action>
2. <next concrete action>
```

"Implemented but not yet verified" is not optional filler — it's where anything gets parked between "written" and "confirmed working." Nothing moves to `COMPLETED.md` until it leaves this list because verification actually ran.

---

## COMPLETED.md

Append-only. Newest entry at the top. Never delete old entries.

**Only log an entry once its verification actually ran and passed** — a test suite that ran and passed, a command that executed and exited 0, or something manually checked and confirmed. "Wrote the module" and "ran it and it worked" are different claims; this file records the second one, not the first. A phase is done when its verification exited 0, not when its tasks were ticked.

```markdown
# Completed Work

## YYYY-MM-DD
- <what was finished, one line> — verified: <how: test run + result, command + exit code, manual check>
- <what was finished, one line> — verified: <how>

## YYYY-MM-DD
- ...
```

If a later session discovers an entry here wasn't actually verified (or the verification was wrong — e.g. a green test suite that didn't cover the real failure mode), don't silently edit the old entry. Add a new dated entry correcting it, so the log stays an honest record of what was believed when.

---

## convo-N.md

One per work session, but **built incrementally, not written once at the end**. Append to it as the session progresses (after each substantive exchange or every few tool-call rounds) rather than saving everything for a single write later — that keeps any given append cheap and means a context-usage flush is a small catch-up, not an expensive full dump. Determine N by counting existing `convo-*.md` files in the folder and using the next integer (first session is `convo-1.md`). If a context-usage flush already created `convo-N.md` earlier in the session, keep appending to that same file rather than starting another.

**Content = the actual conversation, as fully as you can reproduce it — not a condensed summary — but with system-injected boilerplate filtered out.** Skill/tool definitions, deferred-tool listings, and injected `<system-reminder>` blocks are not conversation; don't paste them verbatim. If one changed what you did, say so in a line ("loaded the X skill, which required Y") rather than copying it in. This typically cuts file size substantially with no loss of information a future session actually needs.

Use a short header, then the transcript, then two required closing sections:

```markdown
# Session N — YYYY-MM-DD
_Trigger: [end of session | context-usage flush at ~NN%]_

## Transcript
<the conversation content itself: what the user asked, what was discussed,
decisions made, code/commands run and their real output, and Claude's
responses, in order, as completely as possible — with injected system/skill
boilerplate summarized in a line instead of pasted verbatim>

## Not done / not checked
<mandatory, even when short — write "None; everything above was verified"
if that's genuinely true rather than omitting the section. List anything
that looks finished but wasn't actually verified, and anything assumed
rather than confirmed. This is the section a future session should read
first before trusting anything above as safe to build on.>

## Open questions / carried forward
- <anything unresolved to pick up next time>
```

The header and closing sections keep it skimmable for a future session; the transcript body is the actual record, not a synopsis. If a session was purely exploratory with no concrete output, the transcript can still be short — but it should still be what was actually said, not a one-line gloss, and "Not done / not checked" still applies to anything that was tried but not confirmed.
