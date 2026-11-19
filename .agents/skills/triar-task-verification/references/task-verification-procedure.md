# Task verification procedure

## Purpose

Independently verify one explicitly selected completed TRIAR task. Record a
conclusive pass or proven material non-compliance through `task check`, or
stop blocked when indispensable evidence is insufficient.

## Boundaries

The procedure begins with one explicit task identifier whose implementation was
produced by another executor. It ends with a passed check, a failed check for
proven material non-compliance, or a blocked outcome without a check when
indispensable evidence is insufficient.

The verifier does not fix implementation defects, refine the frozen contract,
select follow-up work or continue execution after the check. Correcting a
failed task belongs to the separate rework procedure and is never part of
verification.

## Prerequisites

- A reusable user connection to the configured store, established once with
  `triar connect` and persisted outside any workspace.
- An initialized TRIAR workspace: the working directory holds a `.triar`
  binding created by `triar workspace init`; initialization performs no store
  authorization and never creates projects.
- An active project selected in that workspace, during `triar workspace init <PROJECT>`
  or later with `triar workspace use <PROJECT>`.
- Explicit `workspace:` and exact `revision:` values identifying the
  implementation workspace and revision to inspect are supplied by the
  external actor, together with any supplemental evidence deliberately
  supplied with the request. Both workspace and revision are required; never
  infer either from cwd, HEAD, branch state or repository discovery.
- The verifier did not perform the implementation being verified.
- The `triar` command is installed and available as `triar` before the
  procedure begins; an unavailable command is a blocked prerequisite, never
  a signal to discover, build or select another executable.

TRIAR lifecycle commits and implementation commits may be produced by different
authorized paths and need not form one linear history. Verify canonical TRIAR
state and the implementation workspace as separate evidence; do not treat that
separation alone as a verification failure.

## Inputs and repository ownership

Verification evidence comes from the explicit `workspace:` and exact
`revision:` values and the material supplied with the request; the canonical
TRIAR context comes from the store. Both workspace and revision are required;
never infer either from cwd, HEAD, branch state or repository discovery.
Missing indispensable external evidence is a blocked finding, never an
invitation to traverse branches, repositories, caches or the filesystem.
`triar sync` and `task check` read and record store state only and establish
no Git relationship — no branch names, topology or ancestry — between store
and implementation repository.

Effective rules may require observable repository state as a verification
condition but grant the verifier no authority to mutate anything: it never
commits, pushes, merges, switches branches or edits the implementation to
improve evidence. If gathering evidence needs an externally owned repository
operation, hand off the concrete request and verify what its owner produces.

## Sequence

Given `<id>` from the external actor:

1. `triar sync` — explicitly synchronize the configured store (store state
   only; the implementation repository is neither touched nor assumed).

2. Before any context read, perform a read-only consistency check that the
   supplied workspace is the intended repository/worktree materialized at the
   supplied exact revision: confirm the workspace is the repository or worktree
   top level and that its checked-out revision equals the supplied full
   revision. A mismatch blocks the run; never switch branches, check out
   another revision, search for substitute workspaces or mutate repository
   state.

3. From the supplied implementation workspace, run ordinary
   `triar task context <id>` — obtain the frozen contract and effective
   context, including repository standards resolved workspace-locally from the
   `.triar` binding and its configured manifest. Both `workspace:` and exact
   `revision:` are required with no ambient fallback: a missing or mismatched
   value stays blocked, neither value is ever inferred from cwd, HEAD, branch
   state or repository discovery, and manifest reading is never duplicated
   outside TRIAR. The task must be in the configured done state; otherwise
   stop and report.

4. Inspect the implementation identified by the explicit `workspace:` and
   `revision:` values and relevant authored commits
   independently. Apply applicable project rules to the work they govern;
   deterministic TRIAR-generated state commits are system commits.

5. Re-run every applicable Action validation, evaluate Success Criteria and
   execute applicable project-wide checks. Record concrete evidence for each
   material finding.

6. Classify the evidence after evaluating Action validations, Success
   Criteria and project-wide checks. If all required checks pass, run
   `task check <id>`. If evidence proves material non-compliance, run
   `task check <id> --failed` to return the task to the configured running
   status. If indispensable evidence is insufficient to decide compliance,
   stop blocked without any `task check`; report what is missing. A suspected
   failure without proof is not a failed check. On a successful check command,
   use its reported lifecycle state as the final outcome; do not read
   `task context` again merely to confirm it. Do not modify the implementation
   to make verification pass, and never cancel work as a consequence of a
   failed check: cancellation is a separate external decision carried out with
   `task cancel`.

7. Hand the outcome outward and stop. TRIAR persists only a conclusive check
   result. Verification results always include `status: passed | failed | blocked`
   and `findings`; passed results have empty findings, failed results list
   concrete verification problems, and blocked results may explain missing or
   insufficient evidence. On a failed check, deliver a concise evidence-backed finding set —
   what failed, where the evidence lives and why it is material — to the
   external actor so rework can be scheduled elsewhere. Implementation fixes,
   rework scheduling and persisted verification reports stay outside this
   procedure.

## Exit behavior

Verification results always include `status: passed | failed | blocked` and
`findings`; passed results have empty findings, failed results list concrete
verification problems, and blocked results may explain missing or
insufficient evidence. Verification still blocks on missing indispensable
implementation evidence, fails on proven material non-compliance and passes
only on conclusive checks.

A passed verification records `task check <id>`. Proven material
non-compliance records `task check <id> --failed`, returns the task to the
configured running state and sends the finding set outward for rework through
`../../triar-task-rework/references/task-rework-procedure.md`.
Insufficient indispensable evidence leaves the task done and stops blocked
without any check; the missing evidence must be supplied externally.
The maintained workflow entrypoints
stay non-overlapping: refinement for unfrozen contracts, execution for
first-pass ready work, verification for done work and rework for running work
after a failed check, with the loop `done -> failed check -> running ->
rework -> done -> verify` requiring no new lifecycle command. Operational or validation
failures in TRIAR itself stop the procedure without substituting manual store
or lifecycle changes.
