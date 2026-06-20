# WORKFLOW.md — the path per task type

One loop, four entry points. The agents and skills change per stack; the loop
doesn't. **Handoffs are files, not chat.**

## Pick the path

| You have…                              | Entry agent          | Produces                              | Then                          |
| -------------------------------------- | -------------------- | ------------------------------------- | ----------------------------- |
| A **new** capability                   | `prd-author`         | `docs/prds/PRD-NNN-*.md`              | decompose → build             |
| A **non-trivial bug**                  | `prd-author` (BR)    | `docs/bug-reports/BR-NNN-*.md`        | decompose → build             |
| A **change to existing behavior**      | `change-requirement` | `docs/change-requests/CR-NNN-*.md`    | approve → build               |
| An **architecture question**           | `architecture-reviewer` | `docs/adrs/ADR-NNN-*.md` (if needed) | feeds the doc above           |
| A **refactor / one-shot**              | (direct task)        | —                                     | build                         |

Rule of thumb: **new thing → PRD · broken thing → BR · different thing → CR.**

## New feature

```
(optional) brainstorm in Cowork
   ↓  "Write a PRD for X"
prd-author → PRD-NNN  (you review + approve)
   ↓  (architecture-reviewer if it raises a design question → ADR)
   ↓  "Decompose this into tasks"
task-manager → docs/tasks/PRD-NNN-tasks.md
   ↓  "Implement TASK-NNN"
feature-builder → code + tests (same commit)
   ↓  (auto)
code-reviewer + test-runner  (parallel)
   ↓  review clean + tests green
release (manual) → smoke test → watch logs
   ↓  "Run retrospective"
retrospective → learns from the work
```

## Behavior change

```
"Write a CR for: <change>"
change-requirement → CR-NNN  (Status: Proposed)
   ↓  (architecture-reviewer if it touches schema/topology → ADR)
   ↓  approver flips Status: Proposed → Approved   ← body is now IMMUTABLE
   ↓  "Implement CR-NNN"
feature-builder → code + tests
   ↓  code-reviewer + test-runner
   ↓  release (manual)
   ↓  "Run retrospective"
```

## The gates (do not skip)

1. **PRD/BR approval** — a human approves before decomposition.
2. **CR gate** — `feature-builder` refuses to build a CR still in `Proposed`.
   After `Approved`, the CR body is immutable; further changes are append-only.
3. **ADR gate** — `feature-builder` stops and spawns `architecture-reviewer` if
   the work surfaces an un-made architecture decision.
4. **Green gate** — no release until review is clean and tests pass.

## Document IDs

Monotonic per type: `PRD-001`, `CR-001`, `BR-001`, `ADR-001`, zero-padded to 3.
Never reuse an ID. Filenames: `CR-007-drop-students-at-grace-end.md`.
