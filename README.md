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
| `wrapup` | End a session cleanly — updates project docs and commits everything in one step. |
| `next-plan` | Just-in-time planner — reads your memory system and writes the next sensibly-scoped plan. |
| `memory-init` / `memory-migrate` | The memory/task system `new-project` installs (dependencies — keep them). |

## Install

**Easiest:** open Claude Code and say *"Read https://github.com/CausewayLabs/cc-starter-kit and help me install these skills."* It'll read this repo and walk you through it.

**Manual:** copy the skill folders into your Claude Code skills directory and restart:

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

The skill files use portable paths (`~/.claude/...`, `~/Projects/...`) so they should work on any machine.
If a skill ever assumes a setup you don't have, just ask Claude: *"adapt these skills to my setup."*

## Golden rule

**Commit early, commit often.** Git is your undo button when working with an AI agent. See `index.html` §1.
