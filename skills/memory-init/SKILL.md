---
name: memory-init
description: >
  Greenfield bootstrap for the memory + task system. Scaffolds the full file set
  (architecture spine, memory files, templates, CLAUDE.md, .context.md router)
  into an empty project. Use when the user says "init memory", "scaffold memory",
  "set up memory system", "initialize memory system", or invokes /memory-init.
  Greenfield only — if the project already has memory artifacts, stop and route
  to /memory-migrate.
---

# /memory-init — Greenfield Bootstrap

You are a memory system installer. Your job is to scaffold the memory + task system into a greenfield project. The output is a living documentation graph that replaces data-layer task tracking: an LLM maintains it; documentation is the substrate.

**One-line goal:** install the files and conventions that let any agent pick up work in this project instantly, without ceremony, by reading `.context.md` first.

---

## Step 0 — Confirm greenfield and locate source templates

Before touching anything:

1. Confirm the user's **target project directory** — where the v2 file set should be installed. If not stated, ask. This is your `TARGET_DIR`.

2. Confirm the **project name** — you'll substitute it into CLAUDE.md. If not stated, infer from the directory name.

3. Check `TARGET_DIR` for pre-existing memory artifacts: `.context.md`, `memory/architecture/`, `memory/`. If **any exist**, stop immediately:

   > "This project already has memory artifacts. Run `/memory-migrate` instead — it handles existing layouts. `/memory-init` only scaffolds empty projects."

4. Locate the **source templates**. They are bundled with this skill at `C:\Users\Ryan\.claude\skills\memory-init\templates\`. You need three files:
   - `templates/CLAUDE.md` — the CLAUDE.md template (substitute `{{PROJECT_NAME}}` with the actual project name)
   - `templates/example-feature-log.md` — canonical feature log format reference
   - `templates/architecture-subdoc-stub.md` — boilerplate for future subsystem subdocs

   If you cannot locate these templates, stop and tell the user where you expected to find them.

---

## Step 2 — Create the directory tree

Create the following directories in `TARGET_DIR`. Use `mkdir -p` or the Write tool's implicit directory creation:

```
TARGET_DIR/
└── memory/
    ├── architecture/          # empty — subdocs grow through dialogue, never pre-instantiated
    ├── handoffs/              # session-continuity escape hatch
    ├── external/              # NotebookLM lenses
    └── templates/             # permanent reference library (copied from to create subdocs/logs)
```

No subsystem subdocs. No paired feature logs. The `memory/architecture/` folder is intentionally empty at bootstrap except for the spine file written in Step 3. `memory/templates/` is a permanent reference library, not init scaffolding — it is never deleted, because its files are copied from every time a new subsystem is born.

---

## Step 3 — Write each file

Write every file below using the **exact content** specified. Do not invent sections, add headers, or expand beyond what is specified. Each file is a deliberate stub — content grows organically through agent dialogue.

### `TARGET_DIR/memory/architecture/architecture.md`

```markdown
# Architecture

*This is the conceptual spine of the project. It describes what the system is, why it is shaped the way it is, and how the major subsystems relate to each other. It is charter, not code map — the Codebase Guide covers code layout and invariants.*

*Subsystem subdocs branch from this spine: each lives at `memory/architecture/<subsystem>.md` and carries a paired feature log at `memory/architecture/<subsystem>-log.md`. Subdocs are prose-driven (Azimuth style) — short italicized orientation header, then pure narrative. They grow through user-agent dialogue; do not pre-instantiate them.*

*When adding a subsystem subdoc: create the file from `memory/templates/architecture-subdoc-stub.md`, fill the orientation header, then write narrative prose. Categories and entries in the paired log grow organically — no pre-baked structure.*

## System Purpose

*(Fill this in: 2-3 sentences on what this system does and why it exists.)*

## Subsystems

*(List subsystems as they are identified. Each entry names the subsystem and links to its subdoc once created.)*
```

### `TARGET_DIR/memory/Codebase Guide.md`

```markdown
# Codebase Guide

*Code-layout router and invariants. This file answers: where does the code live, and what must always hold? Architecture (`memory/architecture/architecture.md`) answers why — this file answers where and what.*

## Layout

*(Describe the top-level directory structure and what each area owns.)*

## Invariants

*Things that must always hold. Promote load-bearing decisions here from feature log completed entries — do not wait for a prune cycle.*

- (none yet)
```

### `TARGET_DIR/memory/Glossary.md`

```markdown
# Glossary

*Business and product vocabulary — terms that have a specific meaning in this project's domain. Code-local jargon goes in Codebase Guide instead.*
```

### `TARGET_DIR/memory/current-plan.md`

```markdown
# Current Plan

*Active plan written by `/next-plan`. Overwritten each time `/next-plan` runs. Check off steps as you complete them. Create a handoff doc in `memory/handoffs/` only if work genuinely gets stuck mid-plan — most plans should resume from this file + recent git activity without ceremony.*
```

### `TARGET_DIR/memory/task-feedback.md`

```markdown
# Task Feedback

*Capture-heuristic refinement log. When the user corrects an auto-logged task (wrong scope, wrong log, shouldn't have been captured), append a note here so the heuristic improves over time.*

*Format:*
```
- (YYYY-MM-DD) Removed/updated: [original item] — reason: [user's correction in brief]
```
```

### `TARGET_DIR/memory/handoffs/` — create the directory only, no files.

### `TARGET_DIR/memory/external/` — create the directory only, no files.

### `TARGET_DIR/memory/templates/example-feature-log.md`

Copy verbatim from the bundled template at `C:\Users\Ryan\.claude\skills\memory-init\templates\example-feature-log.md`. Do not modify content.

### `TARGET_DIR/memory/templates/architecture-subdoc-stub.md`

Copy verbatim from the bundled template at `C:\Users\Ryan\.claude\skills\memory-init\templates\architecture-subdoc-stub.md`. Do not modify content.

### `TARGET_DIR/CLAUDE.md`

Copy from `C:\Users\Ryan\.claude\skills\memory-init\templates\CLAUDE.md`, substituting `{{PROJECT_NAME}}` with the actual project name. All other content must be verbatim — do not add, remove, or reorder sections.

---

## Step 4 — Write `.context.md`

Write `TARGET_DIR/.context.md` with the following content, filling in the project name and tagline:

```markdown
# .context.md — {{PROJECT_NAME}}

*Root router. Read this first every session. It indexes all memory files, architecture docs, and reference templates.*

**Tagline:** *(one sentence: what this project is)*

---

## Architecture

| File | Purpose |
|---|---|
| `memory/architecture/architecture.md` | Conceptual spine — what the system is and why |
| `memory/architecture/` | Per-subsystem design subdocs (empty until subsystems are identified) |

## Memory Files

| File | Purpose |
|---|---|
| `memory/Codebase Guide.md` | Code-layout router + invariants |
| `memory/Glossary.md` | Business/product vocabulary |
| `memory/current-plan.md` | Active plan (written by `/next-plan`) |
| `memory/task-feedback.md` | Capture-heuristic refinement log |
| `memory/handoffs/` | Session-continuity escape hatch (use sparingly) |
| `memory/external/` | NotebookLM lenses |

## Reference Templates

| File | Purpose |
|---|---|
| `memory/templates/example-feature-log.md` | Canonical format reference for feature log entries |
| `memory/templates/architecture-subdoc-stub.md` | Starting point for new subsystem subdocs |

## Commands

| Action | Command |
|---|---|
| Install | *(fill in)* |
| Run | *(fill in)* |
| Test | *(fill in)* |
| Build | *(fill in)* |

---

*To add a subsystem: create `memory/architecture/<subsystem>.md` from `memory/templates/architecture-subdoc-stub.md` and a paired `memory/architecture/<subsystem>-log.md` from `memory/templates/example-feature-log.md`. Do not pre-instantiate — wait until the subsystem is real.*
```

Fill `{{PROJECT_NAME}}` with the actual project name. Ask the user for the tagline if not already provided; it can be left as `*(fill in)*` if they want to fill it later.

---

## Step 5 — Confirm and orient

After all files are written, show the user the created file tree:

```
<TARGET_DIR>/
├── .context.md
├── CLAUDE.md
└── memory/
    ├── architecture/
    │   └── architecture.md
    ├── Codebase Guide.md
    ├── Glossary.md
    ├── current-plan.md
    ├── task-feedback.md
    ├── handoffs/              (empty)
    ├── external/              (empty)
    └── templates/
        ├── example-feature-log.md
        └── architecture-subdoc-stub.md
```

Then deliver this orientation (verbatim or paraphrased — be concise):

> **What to do next:**
> 1. Fill the tagline and Commands table in `.context.md`.
> 2. Write 2-3 sentences in `memory/architecture/architecture.md` → System Purpose.
> 3. When you identify a subsystem, create its subdoc from `memory/templates/architecture-subdoc-stub.md` and its paired log from `memory/templates/example-feature-log.md` — both go in `memory/architecture/`.
> 4. Run `/next-plan` when you're ready to work.

Do not ask follow-up questions. The user can fill the stubs in their own time or during the next session.

---

## Out of scope

This skill does NOT:
- Pre-instantiate any subsystem subdocs or paired feature logs. Those grow through user-agent dialogue.
- Run `/next-plan` or any planning step.
- Migrate existing memory layouts (that's `/memory-migrate`).
- Touch any files outside `TARGET_DIR`.
- Generate content for the stubs beyond what is specified above.
