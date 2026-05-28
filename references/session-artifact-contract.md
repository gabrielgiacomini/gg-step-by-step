# Session Artifact Contract

Every step-by-step run must persist working state under:

`.tmp/step-by-step/YYYY-MM-DD-HHmmss-<session-slug>/`

## Folder layout

```text
.tmp/step-by-step/YYYY-MM-DD-HHmmss-<session-slug>/
├── session-summary.md
├── session-state.json
├── queue.md
├── queue.json
├── evidence-log.md
└── steps/
    ├── step-01-<step-slug>.md
    └── ...
```

Optional subfolders: `artifacts/`, `screenshots/`, `attachments/`.

## Required files

| File | Purpose |
|------|---------|
| `session-summary.md` | Human-readable overview: title, source references, overall status, active step, final summary. |
| `session-state.json` | Machine-readable canonical state: `sessionId`, `sessionSlug`, `startedAt`, `updatedAt`, `sourceType`, `sourcePathOrUrl`, `currentStepId`, `status`, `steps[]`. |
| `queue.md` | Readable ordered queue with prerequisites, statuses, and completion notes. |
| `queue.json` | Machine-readable queue for deterministic resume and handoff. |
| `evidence-log.md` | Append-only journal of preflight checks, commands, outputs, confirmations, blockers, and reopenings. |
| `steps/step-XX-<step-slug>.md` | One file per step containing the packet shown to the user, preflight results, options offered, evidence received, and resolution outcome. |

## Persistence timing

Persist artifacts at these moments:

1. Immediately after session folder initialization.
2. Immediately after queue normalization.
3. Immediately after each active-step packet is produced.
4. Immediately after any evidence changes step status.
5. Immediately before handing off to another skill.
6. Immediately after final completion, defer, or blocked closeout.

## Resume rules

1. Read `session-state.json` first.
2. Reconcile it against `queue.json` and the latest `steps/` records.
3. Continue from the unresolved `currentStepId`.
4. If artifacts disagree, keep the session on the disputed step until the contradiction is resolved.

For the active-step packet structure, see `step-presentation-contract.md`.
