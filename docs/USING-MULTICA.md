# Using multica — monitor & document tasks

This repo is mirrored as a **Project** on our internal multica board (workspace **Turing
Internal**, server `http://localhost:8080`). Use multica as the single UI to **monitor tasks**
(the board) and **document them for future reference** (durable issue history). This file is the
standard for keeping that record complete.

## This repo ↔ multica

| | |
|---|---|
| Workspace  | Turing Internal |
| Project    | **Career Ops Cowork Guide** |
| Project ID | `a061f59e-6c67-46f8-aa2d-8e157c06b129` |
| CLI        | `multica` (on PATH; built from the multica-ai repo at `server/bin/multica`) |

## The rule: every meaningful task becomes an Issue

- **Autonomous agent work** auto-logs (issue → run → comments → status). Nothing to do.
- **Human / Cowork / Claude Code work** — log it so the board stays complete:

**1. Start** — open the issue in this project:
```bash
multica issue create --project a061f59e-6c67-46f8-aa2d-8e157c06b129 \
  --title "<what>" \
  --description "<goal + acceptance criteria; link the docs/ file>"
```
**2. Work** — capture outputs + decisions as comments (this *is* the documentation):
```bash
multica issue comment add <issue-id> --content "<result / decision / link to docs/PRD-…>"
```
**3. Move** — update status as it progresses:
```bash
multica issue status <issue-id> in_progress    # → in_review → done
```
**4. Reference later** — find and re-read any past task:
```bash
multica issue list   --project a061f59e-6c67-46f8-aa2d-8e157c06b129            # this project's board
multica issue search "<keywords>"              # across the workspace
multica issue get    <issue-id>                # description + comments + activity
multica issue runs   <issue-id>                # full execution trace (agent work)
```

## Two layers — don't duplicate

- **multica issue** = the live board + the searchable ledger (status, outputs, links).
- **repo `docs/`** (PRDs, CRs, retrospectives, `.claude/memory/lessons.md`) = the deep specs +
  decisions. Put the depth in `docs/`; **link it from the issue**. Fast scanning + full depth.

## One-time setup (already done for the maintainer)

- `multica login --token <mul_…> --server-url http://localhost:8080`
- For autonomous agents: `multica daemon start` with an authed `claude` (see the multica-ai repo).
