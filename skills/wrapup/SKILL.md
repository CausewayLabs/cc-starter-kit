---
name: wrapup
description: Wrap up a session by updating project documentation and committing changes. Use this skill whenever the user says "wrapup", "wrap up", "wrap it up", "finish up", "close out", or wants to finalize their work before ending a session. Also trigger when the user asks to "document and commit" or "save everything".
---

# /wrapup — Finalize session work

Update project docs and commit all changes in one step.

## Instructions

When the user runs `/wrapup`, perform these steps in order:

---

### 1. Identify active context (branch + worktree)

Run `git worktree list` and `git branch --show-current` to determine:
- Which worktree this session is running in (path)
- The active branch name

Do **not** switch branches. Do **not** merge worktrees into any other branch. The session stays exactly where it is throughout all steps.

---

### 2. Inventory all changes to document

Build a complete picture of what needs to be documented. Run:

```
git log <primary-branch>..HEAD --oneline        # commits ahead of primary
git diff <primary-branch>...HEAD --stat          # files changed vs primary
git status                                        # any uncommitted changes
```

Collect this into a mental change inventory. Documentation in Step 3 must cover every item.

---

### 3. Discover the project's documentation system

Before updating any docs, read the project's documentation map:

1. **Read `CLAUDE.md`** at the project root (or the nearest ancestor that has one). Note any memory layout, file conventions, or documentation rules it defines.
2. **Read `.context.md`** at the project root if it exists. Note which memory files exist and what each one covers.

Use these two files as the authoritative guide for what to update and where. Do not assume a fixed memory layout — every project is different.

---

### 3.5 Reconcile completed plan work (if the project tracks plans)

If the project has `memory/current-plan.md` (and optionally `memory/plans/*.md`), close the
loop on what just finished before writing prose. Read and follow
`~/.claude/skills/shared/reconcile-completed-work.md`.

This is the **warm** call — you still have this session's live context, so you have high
confidence about what was actually done. Use the change inventory from Step 2 as corroboration.
It marks finished `current-plan.md` steps and master-plan streams complete (and matching
`[x]` log checkboxes), keeping the plan files honest *between* sessions even when the next
`/next-plan` is far off. Idempotent; skip silently if there is no plan file.

---

### 4. Update documentation (if it exists)

Only update files that already exist — never create new ones. Coverage must match the full change inventory from Step 2.

**`.context.md`** (project root): If present, update any stale sections given the change inventory. Keep existing structure and voice.

**Memory files**: Follow the conventions discovered in Step 3 exactly. Common patterns (only apply if the project uses them):
- Feature logs (`memory/architecture/<subsystem>-log.md`) — add or update entries for all changes in the inventory; skip if already logged
- Codebase Guide — promote any load-bearing decisions made this session to invariants
- Open threads / backlog — close resolved items, add new ones surfaced this session

**Session continuity entry**: If the project has a `memory/Log.md` (v2 layout), append a dated block at the top:

```
## {YYYY-MM-DD} — session continuity

**What we did:** [1-3 bullets]
**Where we left off:** [1 sentence]
**In progress:** [condensed from open threads]
**Blocked:** [or "nothing blocked"]
**Likely next steps:** [1-2 bullets]
```

Keep it under 10 content lines. If `Log.md` already has 3+ session entries, note: "Log.md has [N] session entries — consider running `/memory-prune`."

If the project uses a legacy `Session State.md`, write there instead (overwrite full file, same format). Do not create it if it doesn't exist.

---

### 5. Commit uncommitted changes

If there are uncommitted changes (including doc updates from Step 4):

1. **Tell the user what you're about to commit to:**
   > "About to commit to branch `<branch-name>`" (and if in a linked worktree: "in worktree `<worktree-path>`")

2. **Wait for the user to confirm** before proceeding. If they say no or redirect, stop.

3. On confirmation:
   - Run `git status` and `git diff` to review all changes
   - Stage relevant files (avoid secrets, build artifacts, `.env` files, etc.)
   - Write a concise commit message summarizing what's being committed
   - Commit to the current branch as-is — no branch switching, no merging

If there are no uncommitted changes, skip silently.

---

### 6. Alert about other open worktrees (informational only)

After handling the current session's context, check whether any other linked worktrees have commits ahead of the primary branch:

```
git -C <other-worktree-path> log <primary-branch>..HEAD --oneline
```

For each unmerged worktree, inform the user:
> "Worktree `<branch-name>` (`<path>`) has N unmerged commit(s)."

This is **informational only** — do not offer to merge them, do not switch to them. The user decides what to do with other worktrees in their own sessions.

If there is only the main worktree (no linked entries), skip this step entirely.

---

### 7. Lens and invariant prompts (v2 projects only)

If the project has a v2 memory bank (`memory/external/` exists or `.context.md` has a Lens Index), ask:

**Lens prompt:** "Any external knowledge captured this session that should become a lens?" — If yes, invoke `/lens-build` now.

**Invariant prompt:** "Any decisions this session that should be promoted to Codebase Guide invariants?" — If yes, edit `memory/Codebase Guide.md` → Invariants section inline now.

Skip both prompts silently if the project has no v2 memory bank, or if the session was purely operational.

---

### 8. Confirm

Briefly summarize what happened:
- Which docs were updated (if any)
- Branch and worktree committed to (if anything was committed)
- Any other open worktrees flagged

Two to four lines maximum.
