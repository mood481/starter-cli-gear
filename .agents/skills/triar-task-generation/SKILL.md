---
name: triar-task-generation
description: Generate validated TRIAR task candidates from one explicitly supplied work description and its sources, following the maintained generation procedure.
---

# TRIAR task generation

Agent entrypoint for the maintained `task-generation-procedure`
(`references/task-generation-procedure.md`). The procedure is the semantic
authority; this skill adds only agent-facing mechanics and never redefines
planning, intake or authority boundaries.

## Caller inputs

- One explicit generation request carrying the requested outcome, its
  boundaries and the deliberately selected source material. This is the whole
  planning input.
- A workspace-local output location for the generated candidate files. If it
  is missing or collides with pre-existing output, stop as blocked.
- The `triar` command installed and available as `triar`. Invoke it directly
  and never select, build or substitute another executable.

Insufficient or contradictory input is a blocked run, never a trigger to
search repositories, branches, caches or the filesystem for more material.

## Mechanics

1. Run `triar sync`, then `triar project context` as a read-only
   effective-context read.
2. Read the request and its supplied material without changing any supplied
   planning artifact. Never widen the corpus beyond what the caller supplied.
   Use configured vocabularies, rules and documents only to author a valid
   TRIAR representation; never widen the planning corpus, enumerate existing
   tasks, or traverse their requirements and stories.
3. Compose candidates under the procedure's closure and representation rules.
   Stop as blocked on unresolved behavioral decisions. Give each candidate
   its deterministic `ref` and carry material behavior from caller-identified
   validated targets into the contract. Keep explicit changes in Actions,
   Success Criteria concise, and local `Validation:` tied to meaningful
   Action proofs; leave project-wide checks in effective context.
4. Materialize only at the caller's output location. Run
   `triar validate task <path>` on the file or directory containing only
   candidates this run created. Stop after valid local output: no `task add`,
   ready transition or continuation into refinement or execution.
5. Keep repository operations outside this skill: it grants no authority to
   commit, push, merge, switch branches or publish, and publication is never
   implicit. Perform only a specific operation the caller explicitly
   delegates.
6. End with a concise handoff: the validated artifact paths and the local
   validation result.
