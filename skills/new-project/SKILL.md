---
name: new-project
description: >
  Bootstrap a brand-new greenfield project with Ryan's preferred layout: a
  code-free root that holds the memory + task system (via /memory-init), git,
  and a run.bat launcher, with all application code living one folder deep in a
  subfolder (default "app"). Use this skill whenever the user wants to start a
  new project, scaffold a new repo, "set up a new project", "spin up a new
  app/server", "create a new project folder", or kick off greenfield work —
  even if they don't explicitly mention memory or folder structure. This is the
  front door for new projects; reach for it before manually mkdir-ing a project
  root. Greenfield only — for an existing project that needs the memory system,
  use /memory-migrate instead.
---

# /new-project — Greenfield Project Bootstrap

You are setting up the skeleton of a new project the way Ryan likes it. The
governing idea is a **clean separation between the project's mind and its body**:

- The **root** is code-free. It holds the memory + task system, documentation,
  `CLAUDE.md`, git, and a single-command launcher. This is the project's mind —
  the place any agent reads first to understand what's going on.
- A **subfolder one level down** holds all the actual application code. This is
  the body. Keeping it isolated means the memory system never gets tangled up in
  build artifacts, framework conventions, or protected directory names, and the
  root stays scannable.

Your job is to create that skeleton and hand back an oriented, ready-to-build
project. You do **not** scaffold the application itself (no `create-next-app`,
no `npm init`) — that's the user's next step, deliberately. You build the shell
and get out of the way.

---

## Step 0 — Gather the essentials

Collect these before touching the filesystem. Infer what you reasonably can and
confirm in one short message rather than interrogating the user.

1. **Project name** — used for the root folder and substituted into the memory
   files. If not given, ask.

2. **Parent directory** — where the project root will be created. Default to a
   `Projects` folder in the user's home directory (e.g. `~/Projects`); if it
   doesn't exist or the user keeps projects elsewhere, ask. The project root will
   be `<parent>/<project-name>`.

3. **Code subfolder name** — default `app`. This is where all code will live.
   Run the clash check below before committing to it.

4. **Stack hint (optional)** — if the user mentions a framework or language
   (Next.js, Android, Rust, etc.), note it. You won't scaffold it, but it
   informs the clash check and the `.gitignore` and `run.bat` you write.

### Subfolder clash check

`app` is the default because it's short and conventional, but it isn't always
safe. Two failure modes to guard against:

- **Windows reserved device names** — `con`, `prn`, `aux`, `nul`, `com1`–`com9`,
  `lpt1`–`lpt9`. These can't be folder names on Windows at all. (`app` is fine
  here; this matters only if a custom name is requested.)
- **Framework-reserved names** — some stacks claim a top-level folder name for
  their own module. The clearest case is **Android**, where the default Gradle
  application module is itself called `app`; nesting your code folder as `app`
  invites confusion with the Gradle module. Other generators may scaffold their
  own `app/`, `src/`, or `lib/`.

If the requested name (default or custom) hits either case, don't silently
proceed. Pick the first safe fallback from this order and **confirm the choice
in one line** before continuing:

1. `application`
2. `<project-name>` (sanitized to a safe folder name)
3. `src`

For a plain web/server project with no conflicting stack, `app` is correct —
use it without ceremony.

---

## Step 1 — Create the root

Create the project root: `<parent>\<project-name>`.

Confirm it doesn't already exist with files in it. If it exists and is
non-empty, stop and ask — this skill is greenfield-only and must never clobber
existing work.

---

## Step 2 — Install the memory system at the root

The memory + task system is Ryan's preferred task-tracking substrate, and it
belongs at the project root. **Invoke the `memory-init` skill** (via the Skill
tool) to install it, passing the project root as the target directory and the
project name you gathered. Let memory-init own this entirely — it is the single
source of truth for the memory layout, and duplicating its file contents here
would let the two drift apart.

Provide memory-init with `TARGET_DIR = <project root>` and the project name up
front so it doesn't re-ask. It will create `memory/` (which includes the
`memory/templates/` reference library), `CLAUDE.md`, and `.context.md` at the
root. Wait for it to finish before continuing — later steps edit the files it
writes.

---

## Step 3 — Create the code subfolder

Create the (empty) code subfolder at `<project root>\<subfolder>` using the name
settled in Step 0.

Drop a single `.gitkeep` file inside so the otherwise-empty folder is tracked by
git and visibly signals "code goes here." Do not scaffold an application — the
folder stays empty by design.

---

## Step 4 — Initialize git with a sensible .gitignore

Run `git init` at the project root.

Write a `.gitignore` at the root covering the common noise. Keep it general
since the app isn't scaffolded yet; the user can extend it once the stack is in
place. Use this baseline, and add stack-specific lines if a stack hint was
given:

```gitignore
# Dependencies
node_modules/
**/node_modules/

# Build output
dist/
build/
out/
**/dist/
**/build/

# Environment & secrets
.env
.env.*
!.env.example

# Logs
*.log
logs/

# OS / editor
.DS_Store
Thumbs.db
.idea/
.vscode/

# Memory system runtime (keep docs, ignore transient)
memory/handoffs/*.tmp
```

Do **not** create a commit unless the user asks — initializing the repo is
enough.

---

## Step 5 — Write the root launcher (run.bat)

Per Ryan's convention, every new app/server project gets a `run.bat` at the root
that starts the project with a single command. Since the app isn't scaffolded
yet, write a thin launcher that changes into the code subfolder and runs the
start command, with the actual command clearly marked as a TODO for the user to
fill once the stack exists:

```bat
@echo off
REM Single-command launcher for <project-name>.
REM All application code lives in the .\<subfolder>\ directory.
cd /d "%~dp0<subfolder>"

REM TODO: replace the line below with the real start command once the app is
REM scaffolded (e.g. "npm run dev", "cargo run", "python -m app").
echo run.bat is not wired up yet. Edit run.bat and set the start command.
```

Substitute `<project-name>` and `<subfolder>` with the real values.

---

## Step 6 — Wire the memory system to the subfolder

memory-init writes its docs assuming code lives at the root. Since code actually
lives one folder down, reconcile two files so the memory system tells the truth
about where things are:

1. **`memory/Codebase Guide.md`** — in the **Layout** section, replace the
   placeholder line with a short description naming the subfolder as the home of
   all application code, and noting the root holds only memory/docs/launcher.

2. **`.context.md`** — fill the **Commands** table's `Run` row with `run.bat`,
   and adjust any layout/orientation text so a fresh agent knows code lives in
   `<subfolder>/`, not at the root.

Keep edits minimal and in the existing voice of those files — you're correcting
a path assumption, not rewriting the docs.

---

## Step 7 — Confirm and orient

Show the final tree (collapse the memory internals memory-init already
reported — just show the top level plus the new pieces):

```
<project-name>/
├── .context.md          # read first
├── .gitignore
├── CLAUDE.md
├── run.bat              # fill in the start command
├── memory/              # the memory + task system (includes templates/ library)
└── <subfolder>/         # all application code goes here (empty)
    └── .gitkeep
```

Then give a brief next-steps orientation, e.g.:

> **Ready to build.** Next:
> 1. Scaffold your app inside `<subfolder>/` (create-next-app, cargo new, etc.).
> 2. Set the start command in `run.bat`.
> 3. Fill the tagline + System Purpose in the memory files when you have a
>    minute, then run `/next-plan`.

Keep it short. Don't re-ask questions — the user fills stubs in their own time.

---

## Out of scope

This skill does NOT:
- Scaffold the application or run any package-manager init. The subfolder is
  intentionally empty.
- Re-implement the memory layout — it delegates entirely to `/memory-init`.
- Create a git commit (just `git init`).
- Migrate an existing project (that's `/memory-migrate`).
- Touch anything outside the new project root.
