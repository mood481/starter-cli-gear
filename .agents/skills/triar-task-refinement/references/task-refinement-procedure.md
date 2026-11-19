# Task refinement procedure

## Purpose

Refine one explicitly selected unfrozen TRIAR task. An unchanged contract
leaves its lifecycle untouched; a changed contract becomes ready when its
current state requires that transition.

## Boundaries

The procedure begins with one explicit task identifier selected by an external
actor. It may change the task contract while the contract remains editable, but
it never executes the task.

Explicitly outside this procedure: task discovery or selection, implementation,
`task start`, `task complete`, independent verification and `task check`,
and failed-verification rework
(`../../triar-task-rework/references/task-rework-procedure.md`).

The procedure grants no authority to commit, push, merge, switch branches
or publish repository content. Any such operation requires a specific
external delegation; publication is never implicit.

## Prerequisites

- A reusable user connection to the configured store, established once with
  `triar connect` and persisted outside any workspace.
- An initialized TRIAR workspace: the working directory holds a `.triar`
  binding created by `triar workspace init`; initialization performs no store
  authorization and never creates projects.
- An active project selected in that workspace, during `triar workspace init <PROJECT>`
  or later with `triar workspace use <PROJECT>`.
- Optional revision material deliberately selected and supplied by the caller.
- The task has not reached the configured running lifecycle state.
- The `triar` command is installed and available as `triar` before the
  procedure begins; an unavailable command is a blocked prerequisite, never
  a signal to discover, build or select another executable.

Refinement consumes the task's current contract and effective context plus
optional revision material deliberately selected and supplied by the caller.
It performs no discovery of planning sources beyond those inputs. When no
supplied input justifies a change, the contract remains unchanged.

Changed contracts are published exclusively through `task update`, never by
editing canonical store files directly. Choose a workspace-local disposable
path during the run only when a change is justified; the file is
non-canonical input. Runtime state files and store internals MUST NOT be
edited manually.

## Sequence

Given `<id>` from the external actor:

1. `triar sync` — explicitly synchronize the configured store.
2. `task context <id>` — read the current contract, status, rules and
   documents. Stop if the contract is already frozen.
3. Refine only the task contract from those inputs. Preserve all unaffected
   metadata and content, including the active contract version; make no
   invented changes and do no additional planning discovery. Keep Mission
   backward-looking, Objective outcome-oriented, Success Criteria independently
   observable and Actions bounded and executable. Use `Validation:` for
   specific local proof that helps complete its Action. Action validation and
   task-level Success Criteria are complementary proof layers, not duplicates
   merely because they inspect the same behavior: preserve an existing
   meaningful local `Validation:` even when that behavior also contributes to
   a Success Criterion, and never remove or rewrite it solely to deduplicate
   Success Criteria. Remove or rewrite a `Validation:` only when it is not
   local to its Action, mechanically repeats a complete Success Criterion or
   project-wide completion check, is incorrect, or adds no distinct proof of
   completing that Action. Do not mechanically copy complete Success Criteria
   or project-wide completion checks into Actions.
4. Compare the result with the current contract. If unchanged, stop without
   materializing a file, validating, running `task update` or `task ready`,
   or changing any canonical or lifecycle state.
5. For a changed contract, choose a workspace-local disposable path during
   this run, materialize the complete revised Markdown there, then validate it
   with `validate task <path>`. Contract-validation diagnostics are part of
   the refinement loop, not automatically terminal blockers. When the
   diagnostics identify a defect that can be corrected from the current
   contract, effective context and caller-supplied material without inventing
   scope or requiring an external decision, revise the disposable contract and
   validate it again. Retry only after a concrete correction; if the same
   defect remains without a justified correction, stop instead of looping.
   Stop immediately when validation fails operationally, or when satisfying a
   diagnostic would require missing external information, a scope decision or
   a reinterpretation of the task. Never publish an invalid contract. Once
   valid, publish with `task update <path>`; the target task is identified
   from the supplied contract, not an operand. Remove the temporary file after
   the update attempt. On update failure, stop without a lifecycle transition.
6. After a successful changed-contract update, if the task is in the
   configured lifecycle start state, run `task ready <id>`. If it was already
   ready, do not attempt a redundant transition.

## Exit behavior

On an operational CLI failure, an uncorrectable contract/input blocker or a
frozen task, clean up any temporary materialization, stop and report the
blocker. A correctable `validate task` diagnostic is not by itself a blocked
outcome: correct the proposed refinement within the existing inputs and
revalidate before deciding whether publication can proceed. Do not repair
lifecycle state manually and do not continue into execution.
