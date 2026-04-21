---
name: sumup
description: Writes a structured context summary of the current Claude Code session to `sumup.md` in the project root, so the user can seamlessly continue after `/clear` or on a later day without re-explaining the project, goal, progress, and open items. ALWAYS use this skill when the user writes `/sumup`, `/sumup <note>`, or says things like "summarize the session so I can continue later", "give me context for the next session", "save the state for tomorrow", or similar.
---

# Sumup — Capture session context for seamless continuation

## When this skill runs

Manually, when the user runs `/sumup` or `/sumup <short note>`, or asks for a handoff snapshot to continue later. **Never automatically.**

## Goal

A persistent `sumup.md` file in the project root, complete enough that the user can paste it as the first prompt in a fresh Claude Code session and Claude continues working without further explanation. The file survives `/clear`, restarts, and day changes.

## Scope boundary

- `/sumup` → ephemeral working state, local to the project, overwritten on the next run.
- Persistent retros / knowledge bases → separate skills, not this one.

`/sumup` **never** writes to a vault and keeps **no history**.

## Procedure

### 1. Determine project root

- Default: current working directory (`cwd`).
- If `cwd` is inside a Git repo, use the repo root instead (`git rev-parse --show-toplevel`).
- Target path is always `<project-root>/sumup.md`.

### 2. Evaluate the session

From the session so far, extract:

- Project context: repo, tech stack, relevant environment.
- Session goal in 1–3 sentences.
- Files, functions, commands, and MCP/tool usage actually touched.
- Decisions with short rationale.
- Open items, next steps, known pitfalls.
- Optional: user-supplied note (`/sumup <note>`) as an extra hint.

**Substance check:** If the session is too thin (no real progress, just chitchat), say honestly "not enough material for a meaningful summary" and ask whether a file should still be written. **Do not invent content. Do not write an empty file.**

### 3. Respect an existing `sumup.md`

If the file already exists, **read it briefly** to carry over unfinished open items into the new version. Then **overwrite completely** — no append, no changelog, no history.

### 4. Write the file

Use exactly this structure. Keep empty sections with "–" rather than dropping them:

```markdown
# Session Summary (YYYY-MM-DD HH:MM)

## Project / Working Directory
- Path: <absolute path>
- Repo / Branch: <if git>
- Relevant environment: <OS, tech stack, specifics>

## Session Goal
<1–3 sentences, what this is about>

## Current State
- <bullet: what was done, with file/function names>
- <bullet: important decision + short rationale>

## Open Items / Next Steps
- ⬜ <concrete next step>
- ⬜ <another step>

## Important Context for Continuation
- Paths / commands / env vars
- Tool- or MCP-specifics that matter
- Known pitfalls
- Skills / agents used

## Entry point for the new session
> Continue at <step>. Context see above. <Concrete instruction if needed>.
```

### 5. Brief confirmation in chat

After writing, respond briefly — no full content dump:

- Path of the written file.
- 1–2 lines describing the core content.
- Hint that the file's content can be pasted as the first prompt of the next session.

## Style rules for the content

- Short, bullet points over prose.
- No filler, no task repetition, no motivational language.
- Concrete: spell out file names, paths, commands.
- Timestamp in the title = current system date + time.
- Match the language the user has been speaking in.
