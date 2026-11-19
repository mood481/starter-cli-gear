---
name: triar-task-rework
description: Correct one running TRIAR task after a failed verification check and complete it without re-checking, following the maintained rework procedure.
---

# TRIAR task rework

Agent entrypoint for the maintained `task-rework-procedure`
(`references/task-rework-procedure.md`). The procedure is the semantic
authority; this skill adds only agent-facing mechanics and never redefines
contract, lifecycle or authority boundaries.

## Caller inputs

- Exactly one task already returned to the configured running state by a
  failed `task check`, plus the verifier's finding set, one implementation
  workspace or revision and any supplemental material the caller deliberately
  supplies.
- The `triar` command installed and available as `triar`. Invoke it directly
  and never select, build or substitute another executable.

Missing findings or indispensable material is a blocked run, never a reason
to search for substitutes or to reinterpret the contract.

## Mechanics

1. `triar sync`, then `triar task context <id>`; the task must be running
   after a failed check or the run stops. Never run `task start`, never
   refine the contract, never discover substitute inputs.
2. Treat the frozen contract plus effective context as the only semantic
   authority; findings are evidence, not requirements. Correct only the
   reported non-conformities and changes strictly derived from them without
   widening scope.
3. Re-run every Action `Validation:` affected by the correction, evaluate
   Success Criteria against the final workspace state, and run applicable
   project-wide completion checks. Run `task complete <id>` only when all
   required checks pass.
4. Never run `task check`, never cancel the task and never select further
   work; a new independent verification belongs to a different actor.
5. If a finding conflicts with the frozen contract or requires a scope
   decision, stop and hand the conflict outward without correcting it.
6. Keep repository operations outside this skill: it grants no authority to
   commit, push, merge, switch branches or publish, and publication is never
   implicit. Perform only a specific operation the caller explicitly
   delegates.
7. End with a concise handoff: task id, lifecycle result and any external
   obligation still pending.
