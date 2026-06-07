---
name: memory-migrate
description: >
  Brownfield bootstrap for the memory + task system. Produces a declarative
  migration guide: describes the target shape, names the standard displacements
  from legacy layouts, and hands off to the agent for per-project judgment. Does
  not move files or parse existing content automatically. Use when the user says
  "migrate memory", "upgrade memory layout", "brownfield memory", or invokes
  /memory-migrate. Greenfield projects should use /memory-init instead.
---

# /memory-migrate — Brownfield Bootstrap

You are a memory migration guide. Your job is to describe what needs to happen to bring an existing project's memory layout into the v2 shape — then hand off to yourself (the executing agent) to apply per-project judgment. You do NOT move files, parse content, or execute any migration steps automatically. Every concrete action is yours to take after reading this guide.

**One-line goal:** produce a clear migration map the executing agent can follow, adapting to whatever the project actually has on disk.

---

## What this skill does and does not do

**Does:** Describe the v2 target shape and standard displacements. Tell you what to look for, what maps to what, and what to drop.

**Does not:** Read project files, move anything, parse existing content, or make per-project decisions. That judgment belongs to you — the agent executing the migration — after you've read the project.

---

## Step 0 — Confirm brownfield and target project

Before proceeding:

1. Confirm the **target project directory** where the migration should happen. If not stated, ask. This is your `TARGET_DIR`.

2. Confirm the **project name** — you'll need it when writing `CLAUDE.md` and `.context.md`.

3. Check `TARGET_DIR` for any existing v2 artifacts: `.context.md`, `memory/architecture/`, `memory/`. If **none exist and the project has no memory artifacts at all**, stop immediately:

   > "This project has no existing memory artifacts — use `/memory-init` instead. It scaffolds the full file set into an empty project."

4. If v2 artifacts are **partially present** (some v2 files exist alongside v1 files), continue — the guide below still applies to the unmigrated portions.

5. Locate the **source templates** in the `task-memory` project (the project this skill lives in). You need:
   - `templates/CLAUDE.md`
   - `templates/example-feature-log.md`
   - `templates/architecture-subdoc-stub.md`

   If you cannot locate these templates, stop and tell the user where you expected to find them.

---

## The v2 Target Shape

After migration, the project should have exactly this file set:

```
TARGET_DIR/
├── .context.md                              # root router: memory file index, lens index, project tagline
├── CLAUDE.md                                # memory + capture heuristic instructions
├── memory/
│   ├── architecture/                        # spine + per-subsystem design subdocs + paired feature logs
│   │   ├── architecture.md                  # conceptual spine (charter, not code map)
│   │   ├── <subsystem>.md                   # prose-driven subsystem design (Azimuth style)
│   │   └── <subsystem>-log.md               # paired feature/task log with categories
│   ├── Codebase Guide.md                    # code-layout router + invariants
│   ├── Glossary.md                          # business/product vocabulary
│   ├── current-plan.md                      # active /next-plan output
│   ├── task-feedback.md                     # capture-heuristic refinement log
│   ├── handoffs/                            # session continuity (escape-hatch only)
│   ├── external/                            # NotebookLM lenses
│   └── templates/                           # permanent reference library (copied from to create subdocs/logs)
│       ├── example-feature-log.md           # format reference for feature log entries
│       └── architecture-subdoc-stub.md      # format reference for new subsystem subdocs
```

**Key principles of v2:**
- `memory/architecture/architecture.md` is charter — it describes what the system is and why, not where the code lives. Subsystem subdocs live alongside it in `memory/architecture/`.
- Each subsystem subdoc in `memory/architecture/` has a paired `-log.md` that carries all planned features, open bugs, and completed work for that subsystem. Categories within the log organize sub-domains.
- `memory/Codebase Guide.md` is positional — code layout and invariants. It is distinct from the architecture spine.
- `memory/current-plan.md` is the only file `/next-plan` writes to. It holds the active plan.
- The `memory/architecture/` folder starts with only `architecture.md` after bootstrap; subsystem subdocs grow through user-agent dialogue.
- Subsystem subdocs and their paired logs are NOT pre-instantiated during bootstrap — wait until the subsystem is real.

---

## Standard Displacements (v1 → v2)

These are the common mappings from legacy v1 memory layouts. Apply them as appropriate — **your judgment is required** because brownfield projects vary.

| v1 File | v2 Destination | Notes |
|---|---|---|
| `overview.md` | `memory/architecture/architecture.md` | Gut the overview into the conceptual spine. Strip positional/code-map content — that goes to `Codebase Guide.md`. Keep the charter: what the system is, why it's shaped this way, how major subsystems relate. Subsystem details promote to `memory/architecture/<subsystem>.md` subdocs. |
| `Open Threads.md` | `memory/architecture/<subsystem>-log.md` | Disperse entries into the appropriate per-subsystem feature log. Each entry should land under the subsystem it belongs to. If a subsystem subdoc doesn't exist yet, create it from `memory/templates/architecture-subdoc-stub.md`. Cross-cutting entries go to the highest common ancestor subsystem. |
| `Goals.md` | `memory/current-plan.md` | Rename and adapt. The current active plan lives here; long-term direction belongs in the architecture spine and feature logs instead. |
| `Log.md` | Drop | Do not migrate. Decision narrative now attaches to completed task entries in feature logs. Load-bearing decisions should be promoted to `memory/Codebase Guide.md` invariants before dropping the file. If `Log.md` contains decisions the project can't afford to lose, extract them first — promote to invariants or attach to the relevant feature log entry — then drop the file. |
| `Map.md` | Drop | `.context.md` already does its job (memory file index + lens index). Do not migrate. If `Map.md` contains anything `.context.md` doesn't cover, fold it in before dropping. |
| root-level `templates/` | `memory/templates/` | Legacy location from an earlier layout. If a `templates/` folder exists at the project root, move it (and its contents) to `memory/templates/`, then update any references — `.context.md` Reference Templates rows, the architecture.md "when adding a subsystem subdoc" pointer, and any subsystem-creation instructions — to the new path. Safe to re-run; if it's already at `memory/templates/`, do nothing. |

---

## Files to Create Fresh

These v2 files have no direct v1 predecessor — create them from scratch or from templates:

| File | Source | Notes |
|---|---|---|
| `.context.md` | Write fresh | Root router. Index all v2 memory files, architecture docs, and templates. Fill the project tagline. Use the structure from `/task-memory-init` as a reference. |
| `CLAUDE.md` | `templates/CLAUDE.md` | Copy from template, substitute `{{PROJECT_NAME}}`. If the project already has a `CLAUDE.md`, merge carefully — the template's capture heuristic and `/next-plan` sections are the priority additions. |
| `memory/task-feedback.md` | Template stub | New in v2. Create as an empty stub with the standard header. |
| `memory/templates/example-feature-log.md` | Copy from task-memory project | Verbatim copy. |
| `memory/templates/architecture-subdoc-stub.md` | Copy from task-memory project | Verbatim copy. |

---

## Your Judgment — What to Do With This Guide

You are the executing agent. This guide names the standard displacements; it cannot account for what this specific project actually has. After reading this guide:

1. **Read the project.** Survey `TARGET_DIR` — list what memory files exist (v1 artifacts, partial v2, custom layouts, anything else).

2. **Map the project to the guide.** Identify which v1 files are present, what they contain at a high level, and which v2 destination each maps to. Flag anything that doesn't fit a standard displacement — those require your judgment.

3. **Handle non-standard artifacts with judgment.** Files not in the displacement table are yours to reason about. Ask: does this content belong in the architecture spine, a subsystem log, the Codebase Guide, or nowhere? Make a call and explain it to the user.

4. **Sequence the work.** Suggested order:
   1. Create `memory/architecture/architecture.md` from `overview.md` content (or from scratch if none exists).
   2. Create `memory/Codebase Guide.md` — pull positional/invariant content from `overview.md` and `Log.md` before dropping them.
   3. Create `memory/current-plan.md` from `Goals.md`.
   4. Identify subsystems — create `memory/architecture/<subsystem>.md` stubs and paired `-log.md` files for each real subsystem.
   5. Disperse `Open Threads.md` entries into the appropriate feature logs.
   6. Drop `Log.md` and `Map.md` (after extracting any load-bearing content).
   7. Write `.context.md` once the file set is stable.
   8. Install `CLAUDE.md` from template (merge if one already exists).
   9. Copy reference templates.

5. **Do not pre-instantiate.** Only create `memory/architecture/<subsystem>.md` subdocs for subsystems that clearly exist in the project. Do not guess or scaffold future subsystems.

6. **Show your work.** After migrating, show the user the resulting file tree and briefly explain each non-obvious mapping decision you made. Non-standard handling especially needs explanation.

7. **Ask before executing destructive steps.** Present a written migration plan to the user and get approval before overwriting, moving, or deleting any existing files. This is especially important when the user has existing content that will be dropped or transformed.

---

## Out of scope

This skill does NOT:
- Read, parse, or transform any existing project files automatically.
- Execute file moves or drops without agent judgment.
- Pre-instantiate subsystem subdocs beyond what the project clearly has.
- Run `/next-plan` or any planning step.
- Touch any files outside `TARGET_DIR`.
- Handle greenfield projects (use `/memory-init` for those).
