# project-context

Give Claude Code (or Claude in general) real, persistent project memory — without relying on a single, ever-growing `CLAUDE.md`.

Most people either don't use `CLAUDE.md` at all, or let it turn into a dumping ground that eventually gets too long to be useful. `project-context` instead keeps a small `.claude-context/` folder of purpose-built files that Claude reads at the start of every session and updates automatically as you work — so a new session (or a fresh instance of Claude) can pick up exactly where the last one left off.

## What it does

At the start of any session, Claude checks for a `.claude-context/` folder in your project (or offers to create one), reads it, and resumes with full context instead of asking you to re-explain everything. Then, **without being asked**, it keeps the files current as you work:

```
.claude-context/
├── PROJECT.md        # what it is, tech stack, architecture, conventions — rarely changes
├── REQUIREMENTS.md   # functional / non-functional requirements, acceptance criteria
├── ROADMAP.md        # phases/milestones, key decisions and why, deferred items
├── STATE.md          # LIVE status: what's in progress, blockers, next steps
├── COMPLETED.md      # append-only log of finished work, newest first
├── convo-1.md        # full transcript of session 1
├── convo-2.md        # full transcript of session 2
└── convo-N.md        # ...one per session
```

**Key behaviors:**
- **Read-before-work** — every session starts by reading the whole folder, not just `CLAUDE.md`.
- **Automatic checkpoints** — `STATE.md` and `COMPLETED.md` get updated at natural pause points (a feature finished, a real decision made, you wrapping up) without you having to ask.
- **90% context-usage safety net** — if Claude's context window usage climbs to 90% or more, it immediately dumps the *full conversation transcript* into the next `convo-N.md` before continuing, so nothing gets lost to compaction or truncation mid-session.

## Installation

**Claude Code — personal skills (all projects):**
```bash
git clone https://github.com/<your-username>/project-context ~/.claude/skills/project-context
```

**Claude Code — single project only:**
```bash
git clone https://github.com/<your-username>/project-context .claude/skills/project-context
```

Claude will pick it up automatically next session — no restart or extra config needed.

**Claude.ai (chat):** upload `SKILL.md` and `references/templates.md` wherever your Claude.ai skill upload flow expects them, or package as a `.skill` file. Note this skill was built primarily for Claude Code; in Claude.ai chat there's no repo filesystem, so it falls back to producing files/artifacts you re-upload instead of writing them directly.

## Why not just use CLAUDE.md?

`CLAUDE.md` is one file trying to be everything: static overview, live status, decision log, and history, all mixed together. That either makes it huge and stale, or people skip it. Splitting those concerns into separate files means each one stays short, current, and skimmable — Claude reads the whole set in seconds instead of parsing one bloated file.

## Contributing

Issues and PRs welcome — especially around edge cases in the 90%-context-threshold detection, since Claude Code doesn't expose a precise numeric signal for that today.

## License

MIT — see [LICENSE](LICENSE).
