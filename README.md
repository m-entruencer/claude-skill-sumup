# /sumup — Session Handoffs for Claude Code

A lightweight [Claude Code](https://docs.claude.com/en/docs/claude-code) skill that writes a structured snapshot of the current session to `sumup.md` in your project root — so you can `/clear` the context window (or close your laptop) and pick up exactly where you left off.

> **Why this matters:** long Claude Code sessions quietly accumulate tokens. Instead of dragging that weight through every follow-up turn, `/sumup` compresses the session into a focused handoff note, you `/clear`, and you paste the note back in. Your next session starts lean and *sharper*, not emptier.

---

## The `/sumup` → `/clear` → paste workflow

```
┌──────────────────────┐     /sumup     ┌──────────────────────┐
│ Long working session │ ─────────────► │ sumup.md written     │
│ Context getting fat  │                │ in project root      │
└──────────────────────┘                └──────────┬───────────┘
                                                   │
                                                   ▼
┌──────────────────────┐  paste sumup.md  ┌──────────────────────┐
│ Fresh session, lean  │ ◄─────────────── │  /clear              │
│ Claude continues     │                  │  Context reset       │
└──────────────────────┘                  └──────────────────────┘
```

Three commands, repeated whenever the session gets heavy:

1. `/sumup` — Claude writes the handoff file
2. `/clear` — Claude Code resets the conversation
3. Paste the content of `sumup.md` as your first prompt

That's it. No config, no background daemons, no external storage.

---

## Why not just keep the long context?

- **Token cost grows with every turn.** A 200-message session pays for its full history on every new turn.
- **Signal dilutes.** Early exploration, failed attempts, and chitchat stay in context and compete with the actual task.
- **Relevance drifts.** After two hours, Claude's attention is split across everything that happened, not just what matters next.

`/sumup` forces you to distill. What's left is the part that's actually load-bearing: goal, current state, open items, pitfalls. The model gets a clean runway.

---

## What gets written

`sumup.md` follows a fixed structure (so it's machine- and human-scannable):

- **Project / Working Directory** — absolute path, repo, branch, environment
- **Session Goal** — 1–3 sentences
- **Current State** — what was done, key decisions + rationale
- **Open Items / Next Steps** — concrete TODOs
- **Important Context for Continuation** — paths, commands, gotchas, tools used
- **Entry point for the new session** — ready-to-paste continuation prompt

The skill overwrites the file every run — no history, no changelog. The point is always-fresh handoff, not an audit trail. (If you want durable session retros, that's a different skill.)

---

## Installation

Claude Code auto-discovers skills in `~/.claude/skills/<skill-name>/SKILL.md`. Clone this repo into that location:

### macOS / Linux

```bash
git clone https://github.com/m-entruencer/claude-skill-sumup.git ~/.claude/skills/sumup
```

### Windows (PowerShell)

```powershell
git clone https://github.com/m-entruencer/claude-skill-sumup.git "$env:USERPROFILE\.claude\skills\sumup"
```

### Windows (Git Bash)

```bash
git clone https://github.com/m-entruencer/claude-skill-sumup.git ~/.claude/skills/sumup
```

Restart any running Claude Code session. The skill appears automatically — no `settings.json` edit needed.

### Language variants

- `SKILL.md` — English (default, loaded by Claude Code)
- `SKILL.de.md` — German original

If you want the German version to be the active one, swap them:

```bash
cd ~/.claude/skills/sumup
mv SKILL.md SKILL.en.md && mv SKILL.de.md SKILL.md
```

---

## Usage

In any Claude Code session:

```
/sumup
```

Or with an extra hint for the next session:

```
/sumup remember to check the migration on staging first
```

Claude writes `sumup.md` to the project root (or repo root if you're inside a Git repo) and prints a short confirmation.

To continue later:

1. Open the project
2. `/clear` (or start a fresh session)
3. Paste the contents of `sumup.md` as your first message

---

## Updating

```bash
cd ~/.claude/skills/sumup && git pull
```

---

## FAQ

**Does this send data anywhere?**
No. The skill only instructs Claude to write a local Markdown file. Nothing leaves your machine beyond what Claude Code normally does.

**Does it work with the Claude Code CLI, desktop, and IDE extensions?**
Yes — anywhere Claude Code loads skills from `~/.claude/skills/`.

**Will it overwrite my existing `sumup.md`?**
Yes, intentionally. The skill reads the existing file first to carry over unfinished open items, then overwrites. No append, no history. Commit or rename it if you need to keep a specific version.

**Can I change the file name or location?**
Fork the repo and edit `SKILL.md` — the structure is plain Markdown with no magic.

**Does this replace proper documentation / retros?**
No. `sumup.md` is a working-state snapshot, not a decision log. For durable engineering retros, use a dedicated retro skill or your usual docs workflow.

---

## License

MIT — see [LICENSE](LICENSE).

---

## Credits

Built by [Entruencer UG](https://entruencer.de/) — shared publicly so the `/sumup` → `/clear` pattern doesn't stay hidden in private repos.

Contributions, issues, and forks welcome.
