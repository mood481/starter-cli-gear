---
name: triar-task-refinement
description: Refine one explicitly selected unfrozen TRIAR task using its effective context and optional caller-supplied revision material, following the maintained refinement procedure.
---

# TRIAR task refinement

Agent entrypoint for the maintained `task-refinement-procedure`
(`references/task-refinement-procedure.md`). The procedure is the semantic
authority; this skill adds only agent-facing mechanics and never redefines
contract or lifecycle semantics.

## Caller inputs

- Exactly one task id that has not reached the configured running state, plus
  optional revision material deliberately selected and supplied by the caller.
- The `triar` command installed and available as `triar`. Invoke it directly
  and never select, build or substitute another executable.

## Mechanics

1. `triar sync`, then `triar task context <id>`; stop if the contract is
   already frozen.
2. Refine only from the current contract, effective context and any supplied
   revision material. Preserve all unaffected metadata and content; make no
   invented changes and do no additional planning discovery. Keep Mission
   backward-looking, Objective outcome-oriented, Success Criteria independently
   observable and Actions bounded and executable. Treat meaningful Action
   `Validation:` and task-level Success Criteria as complementary proof layers:
   preserve an existing local validation even when the same observable behavior
   also contributes to a Success Criterion, and never remove or rewrite it
   solely to deduplicate Success Criteria. Remove or rewrite a `Validation:`
   only when it is not local to its Action, mechanically repeats a complete
   Success Criterion or project-wide check, is incorrect, or adds no distinct
   proof of completing that Action. Do not mechanically copy complete Success
   Criteria or project-wide checks into Action validations.
3. If no justified contract change remains, stop without materialization,
   validation, `task update` or `task ready`; leave all state untouched.
4. For a changed contract, choose a workspace-local disposable path during
   this run, materialize the complete revised Markdown and run
   `triar validate task <path>`. Treat contract-validation diagnostics as
   refinement feedback: when they can be corrected from the current contract,
   effective context and caller-supplied material without inventing scope or
   requiring an external decision, revise the disposable contract and validate
   it again. Retry only after a concrete correction; never publish an invalid
   contract. Stop as blocked only when validation cannot be corrected within
   the authorized refinement inputs, or when the validation command fails
   operationally. Once valid, publish with `triar task update <path>` and
   clean up the temporary file after the update attempt. On update failure,
   clean up, stop and hand back the blocker without changing lifecycle state.
5. After a successful changed-contract update, run `triar task ready <id>` only
   when the task is in the configured start state; never attempt a redundant
   transition.
6. Never execute the task: no `task start`, `task complete` or `task check`,
   and no direct edits of status files or canonical store content.
7. Keep repository operations outside this skill: it grants no authority to
   commit, push, merge, switch branches or publish, and publication is never
   implicit. Perform only a specific operation the caller explicitly
   delegates.
8. End with a concise handoff: task id and resulting status, plus a summary
   of any contract change. If refinement stops blocked, distinguish an
   uncorrectable contract/input blocker from an operational CLI failure.
