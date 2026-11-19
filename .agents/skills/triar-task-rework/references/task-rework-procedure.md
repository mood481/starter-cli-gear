# Task rework procedure

## Purpose

Resume one explicitly selected running TRIAR task after failed verification
and return it to the configured done state for a new independent
verification, preserving its frozen contract and correcting only the
verified non-conformities and their strictly derived effects.

## Boundaries

The procedure begins with one explicit task identifier already returned to
the configured running state by a failed `task check`, plus an explicit
finding set and implementation inputs supplied by an external actor. It ends
at either a blocked outcome or successful `task complete`.

Explicitly outside this procedure: contract refinement, first-pass
execution of ready work, independent `task check` verification, task
selection and discovery, multi-task planning, agent scheduling and
delegation, cancellation, persisted verification reports, project
orchestration and broader workflow-progression decisions.

## Prerequisites

- A reusable user connection to the configured store, established once with
  `triar connect` and persisted outside any workspace.
- An initialized TRIAR workspace: the working directory holds a `.triar`
  binding created by `triar workspace init`; initialization performs no store
  authorization and never creates projects.
- An active project selected in that workspace, during `triar workspace init <PROJECT>`
  or later with `triar workspace use <PROJECT>`.
- The supplied task is in the project's configured running state after a
  failed verification; the context step below establishes it — never assume
  it from prior knowledge.
- One explicit finding set produced by the failed verification, plus one
  implementation workspace or revision explicitly identified for the
  correction and any supplemental material the caller deliberately supplies
  with the request.
- Non-interactive invocation only, with no raw TRIAR repository manipulation:
  never hand-edit status files or store internals.
- The `triar` command is installed and available as `triar` before the
  procedure begins; an unavailable command is a blocked prerequisite, never
  a signal to discover, build or select another executable.

## Inputs and repository ownership

Rework consumes exactly three kinds of material: the canonical TRIAR
context resolved from the store (frozen contract, status, effective rules
and documents), the caller-supplied finding set identifying observed
non-conformities, and the one implementation workspace or revision plus
whatever supplemental material the caller deliberately supplied with the
request. There is no other input channel: missing findings or missing
indispensable external material is a blocked outcome — never a trigger to
search branches, repositories, caches or the filesystem for substitutes.

Verifier findings scope the corrective work but cannot redefine the
contract. The frozen task contract plus effective context remains the sole
semantic authority; findings are evidence, not requirements.

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

Given `<id>`, its finding set and its implementation inputs from the
external actor:

1. `triar sync` — explicitly synchronize the configured TRIAR store. This
   concerns store state only: it lists nothing, and establishes no
   expectation about the implementation repository's branch, history or
   preparation.
2. `task context <id>` — read the current frozen context: the frozen
   contract, rules, documents and references. The task must sit in the
   project's configured running state after failed verification. If it is in
   the configured ready, done, verified or any other state, stop and report
   without lifecycle manipulation. Never run `task start`, never refine the
   contract and never discover substitute inputs.
3. Correct only the reported non-conformities and changes strictly required
   by them, bounded by the frozen contract and effective context. Do not turn
   a verifier suggestion into new scope and do not modify canonical contract
   content. If a finding conflicts with the frozen contract or requires a
   scope decision, stop and hand the conflict outward without correction.
4. Re-run every Action `Validation:` affected by the correction, then
   evaluate Success Criteria and applicable project-wide completion checks
   against the final workspace state.
5. `task complete <id>` — only after every required check has passed and every
   obligation imposed by the effective project context is satisfied:
   project-wide completion checks, applicable quality gates and any
   source-control or commit requirements belong to those rules, never to
   assumptions of this procedure. Never run `task check`, never cancel the
   task and never select further work. Independent verification stays with a
   different actor under the verification procedure. Never use
   post-completion verification to justify done.

## Exit behavior

Exit `0` reports success, `1` a validation failure or blocked outcome, `2` a
usage error and `3` an operational failure. On any non-zero exit, stop and
report; choosing what to do next belongs to the external actor. TRIAR
persists only the lifecycle result; finding sets, scheduling, orchestration
and verification reports stay outside TRIAR. If completing the task would
require promoting workspace content that has not passed the promotion cycle
defined by effective project context, stop as blocked instead of bypassing
that context.
