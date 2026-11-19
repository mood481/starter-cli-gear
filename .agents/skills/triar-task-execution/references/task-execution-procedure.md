# Task execution procedure

## Purpose

Execute one explicitly selected ready task on its first pass from its TRIAR
context to a blocked outcome or successful `task complete`, using only the
stable TRIAR CLI and resolved effective context.

## Boundaries

The procedure begins with one explicit task identifier chosen by an external
actor for an already-prepared task. It ends at either a blocked outcome or
successful `task complete`.

Explicitly outside this procedure: contract refinement, eligible-task discovery,
task selection, multi-task planning, agent scheduling and delegation,
independent `task check` verification, failed-verification rework
(`../../triar-task-rework/references/task-rework-procedure.md`), project orchestration and broader
workflow-progression decisions.

## Prerequisites

- A reusable user connection to the configured store, established once with
  `triar connect` and persisted outside any workspace.
- An initialized TRIAR workspace: the working directory holds a `.triar`
  binding created by `triar workspace init`; initialization performs no store
  authorization and never creates projects.
- An active project selected in that workspace, during `triar workspace init <PROJECT>`
  or later with `triar workspace use <PROJECT>`.
- The supplied task is in the project's configured `ready` state; the
  preflight step below establishes it — never assume it from prior knowledge.
- No workspace or revision caller inputs are required to start: the human
  orchestrator has already chosen the implementation workspace. Work is
  performed in that already-chosen workspace, plus any supplemental files,
  examples or code the caller deliberately supplies with the request.
- Non-interactive invocation only, with no raw TRIAR repository manipulation:
  never hand-edit status files or store internals.
- The `triar` command is installed and available as `triar` before the
  procedure begins; an unavailable command is a blocked prerequisite, never
  a signal to discover, build or select another executable.

## Inputs and repository ownership

Execution consumes exactly two kinds of material: the canonical TRIAR
context resolved from the store (contract, status, effective rules and
documents) and whatever supplemental material the caller deliberately
supplied with the request. The implementation workspace is already chosen by
the human orchestrator and is not a caller input. There is no other input
channel: missing indispensable external material is a blocked outcome —
never a trigger to search branches, repositories, caches or the filesystem
for substitutes.

Store synchronization and lifecycle commands concern the configured TRIAR
store only. They neither imply nor require any Git relationship between the
store and the implementation workspace: generic procedures name no branches,
no topology and no ancestry, and a project may keep its source repository
entirely separate from its store.

Effective rules may make observable repository state or commit metadata a
condition for finishing; a condition is not an authorization. This procedure
grants no authority to commit, push, merge, switch branches or otherwise
mutate or publish any repository, and publication is never implicit. When an
externally owned repository operation remains, hand it off as a concrete
request — repository, revisions, intended effect — and complete only once the
owner has produced the required observable state.

## Sequence

Given `<id>` from the external actor:

1. `triar sync` — explicitly synchronize the configured TRIAR store. This
   concerns store state only: it lists nothing, and establishes no
   expectation about the implementation repository's branch, history or
   preparation.
2. `task preflight <id> --ready` — establish the required pre-start state in
   one read: the task must satisfy every ordinary eligibility check and sit
   in the project's configured ready status. If blocked, stop and report its
   blockers without selecting alternative work.
3. `task start <id>` — the authoritative transition. It synchronizes again,
   rechecks preconditions and freezes the contract.
4. `task context <id>` — read exactly one authoritative effective context: the
   frozen contract, rules, documents and references from the snapshot the
   successful start produced. Execute only this post-start context; no
   pre-start full context read is part of this procedure.
5. Execute every applicable Action and its local `Validation:`, then evaluate
   Success Criteria and applicable project-wide completion checks.
6. `task complete <id>` — only after every required check has passed and every
   obligation imposed by the effective project context is satisfied:
   project-wide completion checks, applicable quality gates and any
   source-control or commit requirements belong to those rules, never to
   assumptions of this procedure. Never use post-completion verification to
   justify done.

## Exit behavior

Exit `0` reports success, `1` a validation failure or blocked outcome, `2` a
usage error and `3` an operational failure. On any non-zero exit, stop and
report; choosing what to do next belongs to the external actor. If completing the task would require
promoting workspace content that has not passed the promotion cycle defined
by effective project context, stop as blocked instead of bypassing that
context.

End every run with a concise execution-to-verification handoff: task id,
lifecycle result, `workspace: <implementation workspace>` and
`revision: <exact implementation revision to verify>` — both expected for
normal committed work — plus any external obligation still pending.
