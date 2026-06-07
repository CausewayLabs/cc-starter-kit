---
name: handoff
description: >
  Create a handoff document so a fresh agent can resume the current task with no prior context.
  Use this skill whenever the user says "/handoff", "hand off", "create a handoff", "context is filling up",
  "write a handoff doc", "save my place", or wants to pass work to a new session.
  Also trigger when the user says they are about to /clear and wants to resume after.
---

# /handoff — Create a handoff document

Capture the current task state into a self-contained handoff doc a new agent can read cold.

## Instructions

### 1. Locate the handoff directory

Check whether a `memory/` directory exists at the project root (where `.context.md` lives, or the git root, or cwd):

- **If `memory/` exists:** write to `memory/handoff/`
- **If no `memory/`:** write to the project root as `handoff/`
- Create the target directory if it doesn't exist.

### 2. Choose a filename

Use this format: `YYYY-MM-DD-HHMM.md`  
Example: `2026-04-24-1530.md`

Get the current date/time from the session context or run: `date +%Y-%m-%d-%H%M`

### 3. Write the handoff document

The doc must let a completely fresh agent (no conversation history, no context) understand:
- What project/repo this is
- What task was being worked on and why
- Exactly where things stand right now
- What is left to do (ordered)
- Any blockers, decisions made, or gotchas
- Which files are most relevant

Use this template — fill every section from your conversation memory. Be dense and specific. No filler.

```markdown
# Handoff — {YYYY-MM-DD HH:MM}

## Project
**Root:** `<absolute path>`  
**Orientation:** Read `.context.md` at root, then `memory/` files listed below.

## Task
<1-2 sentences: what is being built or fixed and why>

## Current State
<What has been completed. Be specific — file names, functions changed, tests passing, etc.>

## What's Left
- [ ] <next concrete action>
- [ ] <step after that>
- [ ] <any remaining steps>

## Key Files
| File | Role |
|------|------|
| `path/to/file` | what it does |

## Decisions & Gotchas
- <Any architectural choice made that must be respected>
- <Any workaround, constraint, or non-obvious behavior>
- <Any blocker and its current status>

## Context Files to Read First
1. `.context.md` — project overview
2. `memory/Log.md` — recent session history (if exists)
3. `memory/Open Threads.md` — in-progress items (if exists)

## Resume Instruction
After reading the above context files, continue with the first unchecked item in "What's Left".
```

### 4. Tell the user

After writing the file, output exactly:

```
Handoff saved: `<relative path to file>`

Run `/clear` then `/handoff-resume` in the new session to pick up from here.
```

No other output.
