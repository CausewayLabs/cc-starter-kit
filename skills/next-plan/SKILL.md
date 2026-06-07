---
name: next-plan
description: >
  Just-in-time planner. Reads the project's architecture spine, subsystem subdocs,
  feature logs, and current-plan.md, then selects and writes the next
  reasonably-scoped plan to memory/current-plan.md. Use when the user says
  "next plan", "what should I work on next", "plan my next session", "what's
  next", "generate a plan", or invokes /next-plan. Also trigger when the user
  completes a plan and asks what to do now.
---

# /next-plan — Just-in-Time Planner

You are the project planner. Your job is to read the current state of the project, apply the selection rubric, and write the next actionable plan to `memory/current-plan.md`. You do not execute tasks — you plan them.

**One-line goal:** write a single-session, actionable plan to `memory/current-plan.md` that a working agent can execute without asking clarifying questions.

---

## Step 0 — Read project context

Read the following files **in order**. Do not skip any. All are inputs to the planning decision.

1. `architecture.md` — conceptual spine; understand what the system is and where it is headed.
2. Every file in `architecture/` — subsystem subdocs (design narrative) and paired feature logs (`*-log.md`). Read them all. Feature logs contain the raw task inventory.
3. `memory/current-plan.md` — the active plan, if any.

If any of these files do not exist yet (e.g. `architecture/` is empty or `memory/current-plan.md` is a stub), note that and continue — absence is valid project state.

---

## Step 0.5 — Reconcile completed work (precursor)

Before deciding what's next, close the loop on what's already done. This is the authoritative
reconciliation gate — it runs every time you plan, so plan state never silently rots.

Read and follow `~/.claude/skills/shared/reconcile-completed-work.md`. It detects which
`current-plan.md` steps and master-plan streams were finished since the active plan was
generated (using the `base-sha` stamp + `git log`), proposes them, and — on confirmation —
marks them complete. It is idempotent; if nothing finished, it returns immediately.

Run this **before** Step 1's active-plan check, so that check sees freshly-reconciled state
(a plan whose last steps just got marked done is correctly treated as complete).

---

## Step 1 — Check for an active plan

After reading `memory/current-plan.md`, determine whether an active plan exists:

- **Active plan:** the file contains a real plan with unchecked steps (i.e. `- [ ]` items that are not yet done).
- **No active plan:** the file is empty, is the default stub with no steps, or all steps are checked off.

**If an active plan exists:**

> "There is an active plan in `memory/current-plan.md` with incomplete steps. Do you want to continue the existing plan, or generate a new one?"

Wait for the user's answer before proceeding. If they say continue, stop here — do not overwrite `current-plan.md`. If they say generate a new one (or the plan is complete/absent), proceed to Step 2.

---

## Step 2 — Apply the selection rubric

Review all feature log entries across all `architecture/*-log.md` files. Identify unchecked items (`- [ ]`). Apply the following rubric **in strict priority order** to select what the next plan should address:

### Rule 1 — Hard blockers first
If any task is explicitly marked as blocking other work (e.g. "blocks X", "required before Y", or depended on by other unchecked items), it goes first. A blocker that is not yet started takes absolute priority over everything else.

### Rule 2 — Foundation before surface
Prefer infrastructure, data layer, contracts, and core logic over dependent features and polish. If two tasks are unblocked, pick the one that other tasks depend on. Examples: schema before UI, auth before protected routes, API contracts before consumers, error handling before edge-case features.

### Rule 3 — Convergence bias
Among otherwise equal candidates, prefer the plan that closes the most open threads. A plan that completes three related tasks in one subsystem beats one that touches a single task in three subsystems — convergence reduces cognitive overhead and shortens the open-thread list.

### Rule 4 — Explicit user signal overrides all
If the user has stated a preference (in this session or visibly in recent context), honor it regardless of the rubric ranking. User signal is the highest-priority input.

**Scope check:** After selecting a candidate set, verify it fits a single session. A single session is roughly 1–4 hours of focused work, 3–8 actionable steps. If the candidate set is too large, scope it down by deferring lower-priority items within the same subsystem. If it is too small, consider adding one adjacent task that Rule 3 supports.

---

## Step 3 — Draft the plan

Write an ordered, scoped, actionable plan. Follow these conventions:

### Format rules

- Ordered numbered steps (not bullets). Each step is an actionable unit of work.
- Steps are scoped tightly: a step should be completable in one focused push, not a multi-hour epic on its own.
- Where a step maps to an existing logged task, include:
  1. A **source reference** on the line above or inline: `[source: architecture/<subsystem>-log.md / <Category Name>]`
  2. The **verbatim task text** copied exactly from the log entry (the checkbox line, minus the `- [ ]` prefix and timestamp). Do not paraphrase — the exact text is used to match and mark the entry complete.
- Where a step is not from a log (e.g. a setup or coordination step you are adding), write it clearly without a source reference.

### Plan header

```
# Current Plan
<!-- Generated by /next-plan on <YYYY-MM-DD> -->
<!-- base-sha: <current HEAD sha> -->

**Focus:** <one-sentence description of what this plan accomplishes>
**Scope:** <subsystem(s) or area(s) being addressed>
**Session target:** single session (~<N> steps)
```

**Stamp the base SHA.** Run `git rev-parse HEAD` and write it into the `base-sha` comment.
This anchors the next reconciliation pass to an exact diff window (`git log <base-sha>..HEAD`)
instead of fuzzy date matching. If not in a git repo, omit the line.

### Example step format

```
1. [source: architecture/auth-log.md / Session Management]
   Implement token refresh logic: on 401 response, attempt silent refresh using stored refresh token; on failure, redirect to login.

2. Set up the test fixture for the auth module (no log entry — prerequisite for Steps 3–5).

3. [source: architecture/auth-log.md / Session Management]
   Add refresh token rotation: issue a new refresh token on every use; invalidate the previous one.
```

---

## Step 4 — Write `memory/current-plan.md`

Overwrite `memory/current-plan.md` with the plan you drafted in Step 3 (including the
`base-sha` stamp). This is the only file you write **when drafting the plan**.

Do not modify feature logs or architecture files *as part of drafting*. (The Step 0.5
reconciliation pass may have already marked completed items in `current-plan.md`, master
plans, and matching log entries — that is expected and separate from drafting.)

---

## Step 5 — Notify the user

After writing the plan, deliver a concise summary to the user:

1. **What plan was selected** — name the focus and scope in one sentence.
2. **Why** — cite the rubric rule(s) that drove the selection. Be explicit: e.g. "Rule 2 (foundation before surface) — the auth token layer must exist before the protected-route middleware can be built." This makes your reasoning auditable.
3. **What was deferred** — briefly name any significant tasks you considered but left out, and why (out of session scope, lower rubric priority, etc.).

Example notification format:

> **Plan written:** Auth token layer (3 steps, `architecture/auth-log.md`)
>
> **Why:** Rule 2 — token storage and refresh are foundational to every protected feature. The dashboard and profile pages (deferred) depend on auth being solid first. Rule 3 reinforced: all three steps are in the same subsystem and closing them opens the path to four downstream tasks.
>
> **Deferred:** Dashboard layout (`architecture/ui-log.md`) — blocked on auth completing; profile settings — Rule 2, depends on user model stabilizing first.

After the notification block, deliver a **plain-language summary** of the plan — 1–2 paragraphs that give an immediate intuition for what the session actually does. Label it clearly:

> **Plain-language summary:**

Write it the way you'd explain the plan standing at a whiteboard — not like a spec. Follow these rules:

- **Chronological and verb-driven.** Start where the work starts and trace it forward. Every sentence is an action: "authors," "ingests," "runs," "converts." No passive voice, no noun-heavy abstractions.
- **Anchor to real named things.** Use the actual file names, script names, subsystem names, and log references from the plan — not vague stand-ins like "the system" or "the pipeline." If the plan says `backfill_impact.py`, say `backfill_impact.py`.
- **Skip jargon walls.** If a technical term is load-bearing, use it and briefly say what it means in passing. If it's not load-bearing, drop it.
- **End with a "so basically" sentence.** The final sentence closes the loop in plain speech — something Ryan could say out loud to explain what the session accomplishes. Example: "So basically it fills the two content holes the last lens audit exposed and then spins up a second lens to prove the pattern generalizes."
- **No length cap** — use as many sentences as it takes to build a working mental model, but don't pad. Two tight paragraphs is the typical target.

---

## Constraints

- When **drafting** (Steps 2–4), `memory/current-plan.md` is the only file you write; logs and architecture files are read-only. The Step 0.5 reconciliation pass is the one exception — it may mark completed checkboxes in `current-plan.md`, `memory/plans/*.md`, and matching log entries.
- Read-only git is allowed (`git rev-parse`, `git log`, `git diff`, `git status`) for the SHA stamp and reconciliation. Do **not** commit, push, or run state-changing commands.
- Do not execute any project tasks or make code changes.
- Do not pre-generate multiple plans and ask the user to choose — select one, write it, explain your reasoning.
- Do not ask clarifying questions unless you genuinely cannot determine the project's state from the files (e.g. all logs are empty and there is no user signal). In that case, ask one targeted question.
- If all feature log items are checked off and there are no open tasks, tell the user: "All logged tasks appear complete. No plan generated — consider running a planning conversation to identify the next feature area."

---

## Out of scope

This skill does NOT:
- Execute tasks or make code changes.
- Create new feature log entries, or delete entries. (It may flip an existing entry's checkbox to `[x]` during Step 0.5 reconciliation — nothing more.)
- Generate new architecture subdocs or subsystem designs.
- Pre-generate multiple plans for the user to pick from.
- Push, commit, or run state-changing shell commands. (Read-only git is permitted — see Constraints.)
