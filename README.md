<p align="center">
  <img src="logo.png" alt="Context logo" width="640">
</p>

<h3 align="center">Persistent project memory for Claude Code</h3>

<p align="center">
  Stop re-explaining your project every session. <code>Context</code> gives Claude a structured, self-updating memory —
  so it picks up exactly where the last session left off.
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="MIT License"></a>
  <img src="https://img.shields.io/badge/Claude%20Code-skill-6E56CF" alt="Claude Code skill">
  <img src="https://img.shields.io/badge/Claude.ai-compatible-6E56CF" alt="Claude.ai compatible">
</p>

---

## The problem

`CLAUDE.md` is one file trying to be everything — project overview, live status, decision log, and history, all mixed together. That either makes it huge and stale, or people stop maintaining it. Either way, every new session starts the same way: you re-explaining what the project is, what's already been decided, and what you were in the middle of.

## What Context does

Context replaces that single file with a small, purpose-built `.claude-context/` folder that Claude reads in full at the start of every session and **keeps up to date automatically as you work** — no reminders needed.

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

## Features

- 🧠 **Read-before-work** — every session starts by reading the whole folder, not just skimming `CLAUDE.md`.
- ✅ **Automatic checkpoints** — `STATE.md` and `COMPLETED.md` update at natural pause points (a feature finished, a real decision made, you wrapping up) without you having to ask.
- 🛟 **90% context-usage safety net** — if context usage climbs to 90% or more, Claude immediately dumps the *full conversation transcript* into the next `convo-N.md` before continuing, so nothing is lost to compaction or truncation mid-session.
- 📁 **One folder per project** — no shared state, no bleed between unrelated projects.
- 🪶 **Lightweight** — plain Markdown files, no database, no external services.

## Install

**Claude Code — all projects (recommended):**
```bash
git clone https://github.com/Narendran-ds/context.git ~/.claude/skills/context
```

**Claude Code — single project only:**
```bash
git clone https://github.com/Narendran-ds/context.git .claude/skills/context
```

That's it — Claude picks it up automatically next session. No restart, no config.

**Claude.ai (chat):** upload `SKILL.md` and `references/templates.md` through your Claude.ai skill upload flow, or package them as a `.skill` file. This skill is built primarily for Claude Code; in Claude.ai chat there's no repo filesystem, so it falls back to producing files/artifacts you re-upload instead of writing them directly.

## How it works

1. **Session start** — Claude checks for `.claude-context/` in your project root. If it's missing, it offers to create one from templates. If it exists, Claude reads `PROJECT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, `STATE.md`, and `COMPLETED.md` in full, plus the most recent `convo-N.md` files.
2. **While you work** — at natural checkpoints (a feature ships, a decision is made, requirements change, you say you're wrapping up), Claude rewrites `STATE.md`, appends to `COMPLETED.md`, and updates `ROADMAP.md`/`REQUIREMENTS.md`/`PROJECT.md` only when something actually changed.
3. **Context running high** — the instant context usage hits ~90%, Claude immediately writes the full transcript to the next `convo-N.md`, updates `STATE.md`, and tells you it did so — a safety net against losing work to compaction.

## Why not just use CLAUDE.md?

| | `CLAUDE.md` | Context |
|---|---|---|
| Structure | One growing file | Purpose-built files, each with one job |
| Live status vs. history | Mixed together | Separated (`STATE.md` vs `COMPLETED.md`) |
| Session transcripts | Not kept | Saved per session (`convo-N.md`) |
| Staleness | Common — gets skipped once it's huge | Each file stays short and current |
| Updates | Manual | Automatic, at natural checkpoints |

## Contributing

Issues and PRs welcome — especially around edge cases in the 90%-context-threshold detection, since Claude Code doesn't expose a precise numeric signal for that today.

## License

MIT — see [LICENSE](LICENSE).
