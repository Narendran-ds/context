---
name: statekeeper
description: Maintains richer, longer-lived project memory than a single CLAUDE.md by keeping a .statekeeper/ folder of purpose-built files — PROJECT.md (overview/stack), REQUIREMENTS.md (specs), ROADMAP.md (phases/decisions), STATE.md (live status), COMPLETED.md (done-work log), and convo-N.md full-transcript files per session. Use at the START of any session — check for this folder (or offer to create one) and read every file before doing other work, so Claude resumes exactly where the project left off. IMPORTANT — watch context-window usage; the instant it hits 90% or more, immediately create the next convo-N.md and dump the full conversation transcript into it as a safety save, even mid-task, before continuing. Also update STATE.md, COMPLETED.md, convo-N.md at natural checkpoints (feature finished, decision made, user wrapping up) without being asked. Trigger on "where did we leave off", project-status requests, high context usage, or substantive multi-step project work needing cross-session memory.
---

# Statekeeper

A lightweight, file-based memory system for long-running projects. Instead of cramming everything into one CLAUDE.md (which either bloats past usefulness or gets ignored), this skill splits project memory into purpose-built files inside a single folder, `.statekeeper/`, at the project root.

```
.statekeeper/
├── PROJECT.md        # what the project is, tech stack, architecture, conventions (rarely changes)
├── REQUIREMENTS.md   # functional/non-functional requirements, specs, acceptance criteria
├── ROADMAP.md        # phases/milestones, key decisions and why, deferred/out-of-scope items
├── STATE.md          # LIVE status: what's in progress, blockers, next steps
├── COMPLETED.md      # append-only log of finished work, newest entry on top
├── convo-1.md        # full transcript of session 1
├── convo-2.md        # full transcript of session 2
└── convo-N.md        # ... one per session (or a mid-session safety dump at 90% context usage)
```

## Core rule: read before you work

At the very start of a session, **before** writing any code or answering the substantive request, check whether `.statekeeper/` exists in the project root (or wherever the user says the project lives).

- **If it exists**: read `PROJECT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, `STATE.md`, and `COMPLETED.md` in full — these are usually short. Skim the last 2-3 `convo-N.md` files (most recent sessions) rather than all of them, unless the project is small enough that reading everything is cheap. Use this to understand what the project actually is, what's already decided, what's in flight, and what's already done — don't re-litigate settled decisions or redo finished work.
- **If it doesn't exist**: briefly tell the user you didn't find one, and offer to set it up (see "Initializing" below). Don't create it silently without mention the first time — after that, updates happen automatically without asking (see "Updating automatically").

## Initializing a new .statekeeper/ folder

When creating the folder for the first time, use the templates in `references/templates.md` for all of these. Fill in whatever the user has already told you; leave sections explicitly marked TBD rather than inventing content.

1. `PROJECT.md` — project overview and tech stack.
2. `REQUIREMENTS.md` — requirements/specs as currently known (can be thin at first).
3. `ROADMAP.md` — phases and any decisions already made.
4. `STATE.md` — empty/starter structure.
5. `COMPLETED.md` — just a header; fills up over time.
6. Do NOT create a convo-N.md yet — that happens at the first checkpoint or end of session (see below).

## Updating automatically

This is the part that most distinguishes this skill from a static CLAUDE.md: **you keep these files current without being asked**. There are two kinds of trigger — a hard, non-negotiable one based on context-window usage, and a set of softer, natural checkpoints.

### Context-window threshold (highest priority — check this constantly)

**If your context window usage reaches 90% or more at any point** (via whatever context-usage indicator is available to you — Claude Code's status line/context meter, a system notice that context is running low, or an approaching auto-compact warning), treat this as an immediate, mandatory checkpoint, regardless of whether you're mid-task:

1. Determine the next `convo-N.md` number (count existing `convo-*.md` files, increment).
2. Write the **full conversation transcript** — everything said in the current session, as completely as you can reproduce it, not a condensed summary — into that `convo-N.md` file. This is a safety dump: the goal is that nothing is lost if the context gets compacted or truncated right after this point.
3. Also do a quick `STATE.md` rewrite (see below) so the live status is current even if the session ends abruptly right after.
4. Tell the user plainly that you did this and why ("context usage is high, so I saved the full conversation to convo-N.md and updated STATE.md before continuing") — this one you DO announce, since it's a signal about session health, not routine housekeeping.

Do this check proactively and repeatedly through a long session — don't wait for the user to ask, and don't skip it because you're in the middle of something.

### Natural checkpoints (softer, routine)

Separately from the context-threshold trigger, also update at ordinary natural checkpoints:

- A feature, bug fix, or discrete chunk of work is finished
- A real decision gets made (architecture choice, library pick, scope change)
- A requirement is added, dropped, or changed
- The user indicates they're wrapping up ("that's it for today", "let's stop here", "good for now", or the conversation is clearly ending)

At each such checkpoint, do the following, in this order:

1. **`STATE.md` — rewrite, don't append.** This file reflects *current* state only. Remove finished items, add new in-progress items and blockers, update "next steps." Stale content actively hurts here, so overwrite freely.
2. **`COMPLETED.md` — append, newest on top.** If something got finished since the last update, add a short dated entry (one or two lines: what was done, not how). Never remove old entries from this file.
3. **`convo-N.md`** — if the context-threshold trigger above hasn't already created one for this session, write it now. Same rule applies: capture the actual conversation content for this session (full transcript, not a trimmed-down synopsis), so a later session — or the 90% safety dump — has real material to read, not just a one-line gloss.
4. **`ROADMAP.md` — edit only when something changes the actual plan** (new phase, a decision that supersedes an earlier one, scope cut). Should change rarely compared to STATE/COMPLETED/convo.
5. **`REQUIREMENTS.md` — edit only when requirements actually change** (added, dropped, clarified). Should also change rarely.
6. **`PROJECT.md` — edit only on real structural change** (tech stack swap, major architecture shift). This is the most stable file of the set; most sessions won't touch it at all.

For routine checkpoints (not the 90% one), don't narrate every file write in detail — a brief one-line mention ("updated state and logged today's session") is enough. Don't interrupt substantive work to do this; batch the updates at the checkpoint.

## File conventions

See `references/templates.md` for the exact starter templates and formatting conventions for each file type (headers, dating convention, entry format). Follow those templates for consistency across sessions — a mix of formats across convo files makes them harder for future-you to skim.

## Where the folder lives

Default to the project's root directory (same level as CLAUDE.md, package.json, etc., if those exist). If the user is working across multiple distinct projects in one environment, each gets its own `.statekeeper/` at its own root — never share one folder across unrelated projects.

## Claude.ai (chat, no filesystem persistence between sessions)

This skill is built primarily for Claude Code, where `.statekeeper/` is real files in the user's repo that persist naturally. In Claude.ai chat, there's no repo to write into by default:

- If the user has a Project with file upload/knowledge, treat those uploaded files the same way — read them at the start, and produce updated versions of `STATE.md` / `COMPLETED.md` / a new `convo-N.md` as files/artifacts for the user to re-upload, since Claude can't write directly into their Project knowledge.
- If there's no persistent storage at all, say so plainly: offer to generate the files as downloads instead of pretending they were saved somewhere durable.
