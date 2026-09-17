---
name: statekeeper
description: Maintains richer, longer-lived project memory than a single CLAUDE.md by keeping a .statekeeper/ folder of purpose-built files — PROJECT.md (overview/stack), REQUIREMENTS.md (specs), ROADMAP.md (phases/decisions), STATE.md (live status), COMPLETED.md (log of *verified* finished work only), and convo-N.md incremental transcript files per session. Use at the START of any session — check for this folder (or offer to create one) and read every file before doing other work, so Claude resumes exactly where the project left off. IMPORTANT — watch context-window usage continuously; the instant it reaches 60-65% (do not wait for 90%, which leaves no headroom to write a good handoff), flush convo-N.md and rewrite STATE.md. Log the transcript incrementally throughout the session rather than as one big write at the end, and strip verbatim system/skill-injected boilerplate before saving it. Every checkpoint must separate what was verified (tests run and passed, commands that exited 0) from what was merely written or claimed — never add to COMPLETED.md without that verification, and always log unverified claims in a "Not done / not checked" section. If a `.planning/` folder (e.g. from GSD) already exists, don't duplicate PROJECT.md/REQUIREMENTS.md/ROADMAP.md — scope down to STATE.md/COMPLETED.md/convo-N.md only. Also update STATE.md, COMPLETED.md, convo-N.md at natural checkpoints (feature finished, decision made, user wrapping up) without being asked. Trigger on "where did we leave off", project-status requests, high context usage, or substantive multi-step project work needing cross-session memory.
---

# Statekeeper

A lightweight, file-based memory system for long-running projects. Instead of cramming everything into one CLAUDE.md (which either bloats past usefulness or gets ignored), this skill splits project memory into purpose-built files inside a single folder, `.statekeeper/`, at the project root.

```
.statekeeper/
├── PROJECT.md        # what the project is, tech stack, architecture, conventions (rarely changes)
├── REQUIREMENTS.md   # functional/non-functional requirements, specs, acceptance criteria
├── ROADMAP.md        # phases/milestones, key decisions and why, deferred/out-of-scope items
├── STATE.md          # LIVE status: what's in progress, blockers, next steps, not-yet-verified work
├── COMPLETED.md      # append-only log of *verified* finished work, newest entry on top
├── convo-1.md        # transcript of session 1, appended incrementally as the session runs
├── convo-2.md        # transcript of session 2
└── convo-N.md        # ... one per session (flushed early if context usage climbs, see below)
```

## Core rule: read before you work

At the very start of a session, **before** writing any code or answering the substantive request, check whether `.statekeeper/` exists in the project root (or wherever the user says the project lives).

- **If it exists**: read `PROJECT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, `STATE.md`, and `COMPLETED.md` in full — these are usually short. Skim the last 2-3 `convo-N.md` files (most recent sessions) rather than all of them, unless the project is small enough that reading everything is cheap. Use this to understand what the project actually is, what's already decided, what's in flight, and what's already *verified* done — don't re-litigate settled decisions, and don't assume something works just because a past session said it was finished without recording a check.
- **If it doesn't exist**: briefly tell the user you didn't find one, and offer to set it up (see "Initializing" below). Don't create it silently without mention the first time — after that, updates happen automatically without asking (see "Updating automatically").

## Coexisting with other planning systems (e.g. GSD)

`PROJECT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, and `STATE.md` are common names — planning tools like GSD write the same four filenames into their own `.planning/` folder. There's no literal path collision (`.statekeeper/` and `.planning/` are different directories), but two authoritative copies of "the roadmap" or "the current state" in one project is worse than having none: nothing says which one is current, and they will drift.

Before initializing, check the project root for `.planning/` or another existing planning-tool folder:

- **If one exists**: don't create `PROJECT.md`, `REQUIREMENTS.md`, or `ROADMAP.md` inside `.statekeeper/` — treat the other tool's versions as authoritative, and read them at session start instead. Scope `.statekeeper/` down to what the other tool doesn't provide: `STATE.md` (live, session-to-session status), `COMPLETED.md` (verified-work log), and `convo-N.md` (transcripts). Mention this once, the first time you set it up this way, so the user knows why the folder is thinner than the full template.
- **If none exists**: initialize the full set as described below.

## Initializing a new .statekeeper/ folder

When creating the folder for the first time, use the templates in `references/templates.md`. Fill in whatever the user has already told you; leave sections explicitly marked TBD rather than inventing content. First, apply the coexistence check above — it determines which of these you actually create.

1. `PROJECT.md` — project overview and tech stack. (Skip if a `.planning/`-style tool already owns this.)
2. `REQUIREMENTS.md` — requirements/specs as currently known (can be thin at first). (Skip if already owned elsewhere.)
3. `ROADMAP.md` — phases and any decisions already made. (Skip if already owned elsewhere.)
4. `STATE.md` — empty/starter structure.
5. `COMPLETED.md` — just a header; fills up over time, and only with verified work (see below).
6. Do NOT create a convo-N.md yet — it's created at the first incremental append (see "Incremental transcript logging" below), not held until end of session.

## Updating automatically

This is the part that most distinguishes this skill from a static CLAUDE.md: **you keep these files current without being asked**. Four mechanisms make this work together: a context-usage trigger that fires early, transcript logging that happens continuously rather than in one big write, a hard separation between verified and unverified work, and softer natural checkpoints.

### Context-window threshold (highest priority — check this constantly)

**If your context-window usage reaches 60–65% at any point** (via whatever context-usage indicator is available — Claude Code's status line/context meter, a system notice that context is running low, or an approaching auto-compact warning), treat this as a mandatory checkpoint. **Do not wait for 90%** — by then there usually isn't enough headroom left to write a good handoff, and writing the transcript is itself expensive enough to matter at that point.

1. Flush `convo-N.md` (see "Incremental transcript logging" below). Because you've been appending throughout the session rather than saving it all for the end, this should be a small final append, not a full dump.
2. Do a `STATE.md` rewrite (see below) so live status is current even if the session ends abruptly right after.
3. Tell the user plainly that you did this and why ("context usage hit ~60-65%, so I flushed the session log and updated STATE.md before continuing") — this one you DO announce, since it's a signal about session health, not routine housekeeping.

Do this check proactively and repeatedly through a long session — don't wait for the user to ask, and don't skip it because you're mid-task. If usage keeps climbing past 65% without a natural pause point, flush again; this is not a one-time gate.

### Incremental transcript logging (ongoing, not just at checkpoints)

Don't treat `convo-N.md` as something written once at the end of a session — that makes the eventual write expensive and risks losing everything if context gets compacted first. Instead, append to it as the session progresses (after each substantive exchange or every few tool-call rounds), so that by the time the context-threshold trigger or a natural checkpoint fires, most of the file already exists and the "write" is just catching up the last few turns.

**When appending, filter out system-injected content — capture the conversation, not the scaffolding around it:**

- Skill/tool definitions, deferred-tool listings, and `<system-reminder>` blocks injected into context are not conversation. Don't paste them verbatim into the transcript. If one materially changed what you did (e.g. loading a skill changed your approach), note that in one line — "loaded the X skill, which required Y" — not by copying its contents.
- Do capture: what the user actually said, what you did and why, code/commands run and their real output, decisions made, and — per the verification rule below — what was verified vs. only claimed.
- When unsure whether something is injected scaffolding or real content, leave it out and summarize its effect instead. A transcript padded with boilerplate is harder to read later, not more complete — filtering it typically cuts file size substantially with no loss of the information a future session actually needs.

### Verification, tracked separately from work done (non-negotiable)

**"Implemented X" and "verified X works" are different claims — never let the first get recorded as if it were the second.** A green test suite tells you what it actually exercised, not what you assumed it covered; code that compiles is not the same as code that behaves correctly under real conditions.

- **`COMPLETED.md` only gets an entry once verification actually ran and passed** — a test suite that ran and passed, a command that executed and exited 0, or something manually checked and confirmed. Work that was implemented but not yet verified belongs in `STATE.md`'s "In progress" (or a "Not verified" note there), never in `COMPLETED.md`. A phase is done when its verification ran and exited 0, not when its tasks were ticked.
- **Every `convo-N.md` entry includes a "Not done / not checked" section**, even when short (write "none — everything above was verified" if genuinely true, rather than omitting the section). List anything that looks finished but wasn't actually verified, and anything assumed rather than confirmed. A log of only successes is not just incomplete — it actively misleads whoever reads it next when deciding what's safe to build on.
- If a later session discovers something marked done in `COMPLETED.md` wasn't actually verified, correct the record with a new entry noting the correction — don't silently edit history.

### Natural checkpoints (softer, routine)

Separately from the context-threshold trigger, also update at ordinary natural checkpoints:

- A feature, bug fix, or discrete chunk of work is finished **and verified**
- A real decision gets made (architecture choice, library pick, scope change)
- A requirement is added, dropped, or changed
- The user indicates they're wrapping up ("that's it for today", "let's stop here", "good for now", or the conversation is clearly ending)

At each such checkpoint, do the following, in this order:

1. **`STATE.md` — rewrite, don't append.** This file reflects *current* state only. Remove finished-and-verified items, add new in-progress items, blockers, and anything implemented-but-not-yet-verified, and update "next steps." Stale content actively hurts here, so overwrite freely.
2. **`COMPLETED.md` — append, newest on top, verified work only** (see verification rule above). Add a short dated entry (one or two lines: what was done, not how). Never remove old entries from this file.
3. **`convo-N.md`** — this should already be mostly current from incremental logging; append anything since the last update, including the "Not done / not checked" section for this session.
4. **`ROADMAP.md` — edit only when something changes the actual plan** (new phase, a decision that supersedes an earlier one, scope cut). Should change rarely compared to STATE/COMPLETED/convo. Skip if a coexisting planning tool owns this file.
5. **`REQUIREMENTS.md` — edit only when requirements actually change** (added, dropped, clarified). Should also change rarely. Skip if owned elsewhere.
6. **`PROJECT.md` — edit only on real structural change** (tech stack swap, major architecture shift). This is the most stable file of the set; most sessions won't touch it at all. Skip if owned elsewhere.

For routine checkpoints (not the context-threshold one), don't narrate every file write in detail — a brief one-line mention ("updated state and logged today's session") is enough. Don't interrupt substantive work to do this; batch the updates at the checkpoint.

## File conventions

See `references/templates.md` for the exact starter templates and formatting conventions for each file type (headers, dating convention, entry format, the required "Not done / not checked" section). Follow those templates for consistency across sessions — a mix of formats across convo files makes them harder for future-you to skim.

## Where the folder lives

Default to the project's root directory (same level as CLAUDE.md, package.json, etc., if those exist). If the user is working across multiple distinct projects in one environment, each gets its own `.statekeeper/` at its own root — never share one folder across unrelated projects.

## Claude.ai (chat, no filesystem persistence between sessions)

This skill is built primarily for Claude Code, where `.statekeeper/` is real files in the user's repo that persist naturally. In Claude.ai chat, there's no repo to write into by default:

- If the user has a Project with file upload/knowledge, treat those uploaded files the same way — read them at the start, and produce updated versions of `STATE.md` / `COMPLETED.md` / a new `convo-N.md` as files/artifacts for the user to re-upload, since Claude can't write directly into their Project knowledge.
- If there's no persistent storage at all, say so plainly: offer to generate the files as downloads instead of pretending they were saved somewhere durable.
