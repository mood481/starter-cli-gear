---
name: triar-task-execution
description: Execute one explicitly selected ready TRIAR task through the maintained execution procedure and complete it only after every required check passes.
---

# TRIAR task execution

Agent entrypoint for the maintained `task-execution-procedure`
(`references/task-execution-procedure.md`). The procedure is the semantic
authority; this skill adds only agent-facing mechanics and never redefines
lifecycle, input or authority boundaries.

## Caller inputs

- Exactly one ready task id, plus any supplemental material the caller
  deliberately supplies. The human orchestrator has already chosen the
  implementation workspace; no workspace or revision inputs are required to
  start. Never discover, rank or select work.
- The `triar` command installed and available as `triar`. Invoke it directly
  with the caller's working directory and never select, build or substitute
  another executable.

An unavailable command or missing indispensable material is a blocked run,
never a reason to search for substitutes.

## Mechanics

1. Run the procedure's sequence through `triar`: `sync`, `task preflight
   <id> --ready`, `task start <id>`, `task context <id>`, then execute every
   Action with its local `Validation:`.
2. Materialize temporary files the caller authorizes at workspace-local
   disposable paths. Never hand-edit status files, canonical store content or
   the hidden store cache.
3. Evaluate Success Criteria, applicable project-wide completion checks and
   any observable repository obligations from the effective context against
   the final workspace state. Do not run `task complete <id>` until every
   required observable state exists; when an external repository operation is
   still needed, hand off the concrete request and wait for its owner.
4. Never run `task check` on work you executed; independent verification
   belongs to a different actor.
5. Keep repository operations outside this skill: it grants no authority to
   commit, push, merge, switch branches or publish, and publication is never
   implicit. Perform only a specific operation the caller explicitly
   delegates.
6. End with a concise execution-to-verification handoff: task id, lifecycle
   result, `workspace: <implementation workspace>` and
   `revision: <exact implementation revision to verify>` — both expected for
   normal committed work — plus any external obligation still pending.
