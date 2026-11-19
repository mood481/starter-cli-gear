# Task generation procedure

## Purpose

Turn an explicitly supplied planning scope and the supporting sources its
caller selected into the smallest coherent set of finished TRIAR task
candidates, validate the output locally and stop before canonical intake.

## Boundaries

The procedure begins with one explicit generation request from an external
actor and only the material supplied with it. It produces validated
candidate artifacts in the canonical pre-intake representation and nothing
else.

Explicitly outside this procedure: planning-corpus discovery, retrieval,
filtering or reduction (owned by the caller, see "Ownership"); canonical
intake (`task add`), persistent ID allocation, lifecycle readiness
(`task ready`), refinement of existing tasks, execution, verification and
any other store mutation. Publishing validated candidates is a separate
decision owned by the requesting human or orchestrator under its own
policy.

The procedure grants no authority to commit, push, merge, switch branches
or publish repository content. Any such operation requires a specific
external delegation; publication is never implicit.

## Prerequisites

- A reusable user connection to the configured store and an initialized
  TRIAR workspace with an active project.
- One explicit generation request carrying the work description: the
  requested outcome, its boundaries and the supporting source material the
  caller deliberately selected. This is the whole planning input; the
  procedure never goes looking for more.
- A caller-supplied workspace-local output location for the generated
  candidate files. A missing location or collision with pre-existing output
  blocks generation. The files are disposable until a later policy passes
  them to `task add`.
- The `triar` command is installed and available as `triar` before the
  procedure begins; an unavailable command is a blocked prerequisite, never
  a signal to discover, build or select another executable.

## Inputs and sources of authority

Generation is explicit-input-bound. The only material that may drive a
candidate is:

1. The generation request: its requested outcome and boundaries are the
   scope; anything outside them is not.
2. The source material deliberately supplied with the request by the
   caller: local files, excerpts or references handed in explicitly.
3. The mandatory read-only `triar project context` result: the project
   descriptor and its effective rules and documents. It constrains TRIAR
   representation and applicable contract conventions only; it cannot add
   planning work, widen scope, enumerate existing tasks or trigger traversal
   of their `requirements` or `stories`. The hidden store cache and repository
   checkout are never read directly.

Constraints that limit what may be produced (boundaries, prohibitions,
non-goals) must be kept separate from procedural instructions that direct
how work is carried out (quality gates, conventions); the former scope the
output, the latter bind the generated contracts. Preserve source
terminology in generated content instead of paraphrasing it into new
vocabulary. Treat supplied planning artifacts as read-only. If supplied
material is insufficient to close the requested outcome, two sources
contradict each other, or a material behavioral decision remains unresolved,
stop and report the gap as a blocked outcome; never search the repository to
fill it, silently reconcile, invent scope or invent architecture.

Every material planning constraint used by generation must be traceable to
the request or a source the caller supplied. Project context constrains only
the TRIAR representation and applicable contract conventions.

## Ownership

Source discovery, retrieval, filtering, temporary materialization of source
inputs, and prompt or context-budget decisions belong to the caller: a human, an
orchestrator, or a future agent skill that automates this procedure. The
procedure itself is the portable semantic workflow those agents automate —
it neither replaces their selection policy nor duplicates it. Selection may
happen upstream under any policy the caller judges appropriate, but once
this procedure begins its planning corpus is exactly the supplied material
and never widens on its own. The fixed invariants are: generation consumes
what was supplied, authors the canonical candidate representation,
validates locally and stops.

## Sequence

Given the generation request and its supplied material:

1. `triar sync` — explicitly synchronize the configured store so local
   project reads and candidate validation run against current context.
2. Run `triar project context` and use its descriptor, rules and documents
   only to constrain TRIAR representation. This read never triggers planning
   discovery.
3. Read the request and the material supplied with it; separate scope
   constraints from procedural instructions. If the explicit input is
   insufficient, contradictory or leaves a material behavioral decision
   unresolved, stop blocked without discovering additional planning context.
   Require an explicit output location free of pre-existing candidate output
   before materializing anything.
4. Decide the decomposition. Start from one task and split only when the
   supplied material establishes a meaningful closure boundary. Explicit
   signals: distinct final outcomes; work independently completable and
   verifiable; significant sequential dependencies; separate contracts or
   components with their own closure; a prospective contract becoming
   overloaded with unrelated actions or repeated "and also" scope; and
   phases the supplied sources themselves require. The target is the
   fewest tasks that stay closed, verifiable and reasonably independent
   without overloading a contract. Never force a fixed task count, and
   never expand planning scope merely because additional work might be
   useful.
5. Compose each finished candidate in the canonical pre-intake
   representation. The TOML frontmatter carries the applicable task
   metadata — a single-line `title`, a deterministic local `ref` slug derived
   from its title (resolve collisions deterministically within the run), and,
   only when the project configures the vocabulary, `type`, `labels`,
   `milestone` or `release`; optional `requirements` and `stories` may
   reference only files already existing within the project, and only when
   the caller's supplied material identified them — plus `depends` when
   supplied material establishes it. The body carries the final Mission,
   Objective, Success Criteria and bounded
   Actions, and its first H1 is exactly `# <title>`. No `schema` and no
   task `id`: persistent identity is allocated by intake, not by
   generation. Each candidate must remain semantically complete after
   temporary inputs disappear: carry the material behavior needed to
   implement and verify its outcome into its own contract. Preserve every
   material behavior of a caller-identified validated target. Put explicit
   implementation changes in Actions, keep Success Criteria concise and
   observable, and add local `Validation:` only for meaningful Action proofs.
   Do not duplicate project-wide completion checks in either field.
6. Bound the dependencies. Represent dependencies only when the supplied
   material establishes them. Every member has a `ref`; depend on another
   generated member through `@ref` when the supplied material requires it;
   these local dependencies must be acyclic. Preserve canonical task
   dependencies already present in the supplied material unchanged.
   Generation never discovers or infers additional dependencies, and does
   not encode execution plans, verification evidence or scheduler semantics
   inside contracts.
7. Validate the generated output locally and stop:

   ```sh
   triar validate task <path>
   ```

   `<path>` is the new single candidate file or a new directory containing
   only first-level candidate files created by this run. Never validate a
   directory containing unrelated pre-existing candidates as this run's
   output. Correct generation defects and re-validate until the whole new
   output is valid. Then end the procedure, reporting the validated
   artifacts and the validation result. Do not run `task add`, do not
   mark anything ready and do not continue into refinement or execution.

## Candidate representation

One representation covers both a standalone candidate and a directory set;
it needs no companion manifest and no second task schema. Every candidate
has a deterministic local `ref`, including a standalone candidate.
Materialized at intake, a candidate becomes an ordinary `task/v1`
contract: the supported schema, the assigned canonical id and the
identity-bearing H1 are added by TRIAR, and titles must already be
representable in that canonical form.

The two members below illustrate the candidate representation and local
dependency syntax. Their placeholder bodies must be replaced with closed,
verifiable contracts before validation.
The producer declares the local `ref` and the consumer resolves
`@example-bounded-outcome`.
A candidate never depends on its own `ref`: set dependencies stay acyclic.
Replace the illustrative titles, refs and milestone with values supported
by the target project's supplied input and configuration.

```markdown
+++
title = "Example bounded outcome"
ref = "example-bounded-outcome"
milestone = "Bootstrap"
+++

# Example bounded outcome

## Mission

Why the requested work exists, traced to the sources that required it.

## Objective

The closed, observable outcome the contract commits to.

## Success Criteria

Independently verifiable results, traceable to authoritative sources.

## Actions

### 1. Do the bounded work

The prescribed work, without scope the sources did not establish.
```

```markdown
+++
title = "Example dependent outcome"
ref = "example-dependent-outcome"
milestone = "Bootstrap"
depends = ["@example-bounded-outcome"]
+++

# Example dependent outcome

## Mission

Why the dependent work exists, traced to the ordering the sources set.

## Objective

The producer outcome above is consumed by this closed outcome.

## Success Criteria

The dependency is respected and results remain traceable.

## Actions

### 1. Consume the producer outcome

Build on the prior task without adding scope the sources did not
establish.
```

## Determinism

The method is deterministic in its inputs and rules: the same request, the
same supplied material and the same mandatory project context read go
through the same authority, decomposition and composition rules. Two
equally valid executions need not produce byte-identical decompositions;
determinism constrains what may influence a decision, not the exact
wording of every outcome.

## Exit behavior

Insufficient supplied material, an unresolved behavioral decision, a missing
or colliding output location, a contradiction between the request and its
supplied sources, or a candidate set that will not validate locally is a
blocked outcome: stop and report it without searching the repository for
replacements, without narrowing the requirement and without emitting
unvalidated artifacts. Never mutate TRIAR canonical state from this
procedure; only `task add`, under the caller's own review policy, turns
validated candidates into canonical tasks.

## Maintenance

This procedure is general and project-agnostic: the same text must work
for TRIAR and non-TRIAR projects once combined with their respective
effective rules and documents, and environment-specific generation
constraints belong in those project resources, not here. Any CLI or
contract change that alters this procedure's supported flow — the candidate
representation, dependency forms, the project-read role, the validation
command or the stop boundary — MUST update this procedure in the same task.
