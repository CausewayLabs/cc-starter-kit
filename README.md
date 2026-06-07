# Claude Code Starter Kit

A curated set of Claude Code skills and workflows, packaged for getting started fast.

**👉 Start here:** open [`index.html`](index.html) in any browser (works fully offline) for the illustrated knowledge-transfer guide.

## What's inside

| Skill | What it does |
|-------|--------------|
| `new-project` | Scaffolds a clean, git-initialized greenfield repo (code-free root + `app/` subfolder + memory system). |
| `brainstorm` | Refines a fuzzy idea into a structured feature plan, then coordinates the agent pipeline. |
| `opus-review` / `team-lead` / `qa-review` | The agent-pipeline phases: plan review → build → QA. Driven automatically by `brainstorm`. |
| `orchestrator` | Lighter delegation mode — Claude coordinates developer subagents and reviews their work. |
| `handoff` / `handoff-resume` | Save and restore your place across sessions when the context window fills up. |
| `memory-init` / `memory-migrate` | The memory/task system `new-project` installs (dependencies — keep them). |

## Install

Copy the skill folders into your Claude Code skills directory and restart:

```powershell
# Windows (PowerShell)
Copy-Item -Recurse skills\* $env:USERPROFILE\.claude\skills\
```

```bash
# macOS / Linux
cp -r skills/* ~/.claude/skills/
```

Then try `/new-project` or `/brainstorm` inside Claude Code.

## Note on paths

Some `SKILL.md` files reference personal paths (e.g. `D:\Projects`, `C:\Users\Ryan\...`).
Edit them to match your own machine, or just ask Claude: *"adapt these skills to my setup."*

## Golden rule

**Commit early, commit often.** Git is your undo button when working with an AI agent. See `index.html` §1.
