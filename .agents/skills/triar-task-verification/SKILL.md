---
name: triar-task-verification
description: Independently verify one completed TRIAR task, recording conclusive checks or blocking on insufficient evidence, following the maintained verification procedure.
---

# TRIAR task verification

Agent entrypoint for the maintained `task-verification-procedure`
(`references/task-verification-procedure.md`). The procedure is the semantic
authority; this skill adds only agent-facing mechanics and never redefines
verification or lifecycle semantics.

## Caller inputs

- Exactly one completed task id whose implementation another executor
  produced, plus explicit `workspace:` and exact `revision:` values identifying
  the implementation to inspect and any supplemental evidence. Both workspace
  and revision are required inputs; never infer either from cwd, HEAD, branch
  state or repository discovery. The verifier must not have performed the
  implementation.
- The `triar` command installed and available as `triar`. Invoke it directly
  and never select, build or substitute another executable.

Missing indispensable evidence is a blocked finding, never an invitation to
traverse branches, repositories, caches or the filesystem.

## Mechanics

1. `triar sync`. Before any context read, perform a read-only consistency
   check that the supplied workspace is the intended repository/worktree
   materialized at the supplied exact revision: confirm the workspace is the
   repository or worktree top level and that its checked-out revision equals
   the supplied full revision. A mismatch blocks the run without switching
   branches, checking out another revision, searching for a substitute
   workspace or mutating repository state.
2. From the supplied implementation workspace, run ordinary
   `triar task context <id>` using the caller-supplied explicit workspace and
   exact revision; the task must be in the configured done state or the run
   stops. Both `workspace:` and exact `revision:` are required with no ambient
   fallback: a missing or mismatched value stays blocked without reading the
   ambient working tree, and neither value is ever inferred from cwd, HEAD,
   branch state or repository discovery. Repository standards resolve through
   this ordinary workspace-local TRIAR context read; never reimplement or
   duplicate manifest reading.
3. Inspect the implementation and authored commits independently, re-run
   every applicable Action validation and project-wide check, evaluate Success
   Criteria against the final evidence, and record concrete evidence for each
   material finding.
4. Record only a conclusive outcome: run `triar task check <id>` when every
   required check passes; run `triar task check <id> --failed` only when
   sufficient evidence proves material non-compliance. If evidence is
   insufficient to determine compliance, stop as blocked without running any
   `task check`. Never modify the implementation to make verification pass,
   never fix defects and never cancel work — cancellation is a separate
   external decision.
5. TRIAR persists only the recorded check result. On a failed check, hand a
   concise evidence-backed finding set — what failed, where the evidence
   lives and why it is material — back to the caller for rework scheduling;
   never persist a verification report and never execute the rework yourself.
6. Keep repository operations outside this skill: it grants no authority to
   commit, push, merge, switch branches or publish, and publication is never
   implicit. Perform only a specific operation the caller explicitly
   delegates.
7. End with a concise verification result containing `status: passed | failed | blocked`
   and `findings`: passed results have empty findings, failed results list
   concrete verification problems, and blocked results may explain missing or
   insufficient evidence. Include the task id and check result alongside this
   contract, or the indispensable evidence missing from a blocked run.
