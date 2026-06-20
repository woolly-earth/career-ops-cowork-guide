# CLAUDE.md — career-ops-cowork-guide

Project context loaded automatically by Claude Code / Cowork. Keep it short, current, and true.

## What this project is

A **community implementation guide** — a static GitHub Pages site plus a README — documenting
how to run [career-ops](https://github.com/santifer/career-ops) (santifer's open-source AI
job-search command center) inside **Claude Cowork** and **Claude Code**, with the customization
patterns for a multi-track senior search. This is a **docs / content repo, not a code app**.
Independent and unaffiliated with Anthropic or the career-ops author (MIT, maintained by
woolly-earth). The one outcome it must get right: a reader can stand up career-ops in Cowork in
~1 hour by following it.

## Stack

| Item            | Value                                                          |
| --------------- | -------------------------------------------------------------- |
| Type            | Static site (GitHub Pages) + Markdown guide                    |
| Files           | `index.html`, `assets/style.css`, `README.md`, `examples/*`    |
| Build           | none — `.nojekyll`, served as-is by GitHub Pages               |
| Source of truth | `README.md` is the canonical guide; `index.html` mirrors it    |

> Edits here are almost always **content** (README / HTML / examples), not code. The build
> pipeline below (prd-author → feature-builder → test-runner) is available but usually overkill —
> most changes are direct, surgical doc edits. Keep `README.md` and `index.html` in sync.

## Conventions

> Adapted from andrej-karpathy-skills (MIT). Bias toward caution over speed; for trivial edits,
> use judgment. **Human-owned** section.

1. **Think before writing.** State assumptions; if multiple readings exist, surface them rather
   than pick silently; stop and name what's unclear instead of guessing.
2. **Simplicity first.** Say it in the fewest words that are still correct. No filler.
3. **Surgical changes.** Touch only what the request requires; match the existing voice and
   formatting; don't restyle adjacent prose.
4. **Goal-driven.** Turn vague asks into a verifiable outcome ("make step 3 clearer" → "a new
   reader can complete it without questions") and check against it.

## The one rule

**Two tools, one folder — never write the same file at the same time.** Plan in Cowork →
build/edit in Claude Code → review. Sequential. Handoffs travel through named files in `docs/`,
never through chat. (Fitting, since this guide is itself *about* the Cowork + Code workflow.)
See `WORKFLOW.md`.

## How to drive the system

Optional for a docs repo, but available:
- "Write a PRD for …" → `prd-author` · "Write a CR for …" → `change-requirement`
- "Decompose this into tasks" → `task-manager` · "Implement TASK-NNN" → `feature-builder`
- "Review the diff" → `code-reviewer` · "Run retrospective" → `retrospective`

## gstack

**Required** for AI-assisted work in this repo — enforced by `.claude/hooks/check-gstack.sh`
(wired via `.claude/settings.json`): Skill use is blocked unless gstack is installed globally at
`~/.claude/skills/gstack`. Install:
`git clone --depth 1 https://github.com/garrytan/gstack.git ~/.claude/skills/gstack && cd ~/.claude/skills/gstack && ./setup --team --no-prefix`

Use /browse from gstack for all web browsing; never use mcp__claude-in-chrome__* tools.
gstack provides the build/QA/ship slash commands (/review, /ship, /qa, /design-review,
/investigate, /retro, /learn, …).

## Lessons (retrospective-owned)

> Maintained by the `retrospective` agent (append-and-supersede). Do not hand-edit below.

<!-- LESSONS:BEGIN -->
_(none yet)_
<!-- LESSONS:END -->
