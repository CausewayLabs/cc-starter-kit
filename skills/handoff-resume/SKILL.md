---
name: handoff-resume
description: >
  Resume a task from the most recent handoff document. Use this skill whenever the user says
  "/handoff-resume", "resume from handoff", "pick up where I left off", "load the handoff",
  or starts a fresh session after running /handoff in a previous session.
  This is always the first thing to run in a new session after a /clear.
---

# /handoff-resume — Resume from handoff

Orient a fresh agent using the most recent handoff doc and surrounding context files.

## Instructions

### 1. Find the most recent handoff doc

Check these locations in order:
1. `memory/handoff/` relative to cwd
2. `handoff/` relative to cwd
3. Walk up to git root and check both paths there

List files in the handoff directory and pick the one with the latest timestamp in its filename (`YYYY-MM-DD-HHMM.md` format). If filenames are ambiguous, use filesystem modification time.

To find the git root: `git rev-parse --show-toplevel`  
To list handoff files: `ls -t memory/handoff/` or equivalent

### 2. Read the handoff doc

Read the full file. Extract:
- **Project root path**
- **Task summary**
- **Current state**
- **What's left** (the checklist)
- **Key files**
- **Decisions & gotchas**
- **Context files to read**

### 3. Read orientation files

From the handoff doc's "Context Files to Read First" list, read each file that exists:
- `.context.md` at project root — always read this if it exists
- `memory/Log.md` — read the most recent entry only (top of file)
- `memory/Open Threads.md` — read In Progress and Blocked sections only
- Any other files explicitly listed in the handoff

Do not read files not listed. Keep reads targeted.

### 4. Confirm orientation and resume

Output a brief orientation summary (4-8 lines max):

```
Resumed from: `memory/handoff/YYYY-MM-DD-HHMM.md`

**Task:** <one sentence>
**Done:** <what's already complete>
**Up next:** <first unchecked item from What's Left>
**Watch out for:** <most important gotcha, or "nothing flagged">
```

Then immediately begin working on the first unchecked item from the handoff's "What's Left" list — do not ask the user what to do next unless the handoff is ambiguous or the first step requires a decision.

### 5. If no handoff doc is found

Tell the user:
```
No handoff doc found in memory/handoff/ or handoff/.
If you have a specific file path, share it and I'll load it directly.
```
