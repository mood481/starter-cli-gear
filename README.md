# @mood481/starter-cli-gear

Reusable starter for CLI Starter.

This is a **starter-authoring repository**, not the application or module it will
generate. Complete `starter.yaml` and the selected template sources before publishing.

## Contents

- `starter.yaml`: starter identity, enabled modes, templates, overlays,
  variants, and placeholder requirements.
- `template/`: common neutral source files with `__PLACEHOLDER__` tokens.
- `modes/standalone/overlay/`: standalone-only root files such as `.gitignore` and `.editorconfig`.
- `modes/module/overlay/`: module-specific README and any future overrides.

- `variants/`: optional overlays owned by the starter.
- `docs/render.yaml`: request for the configured default mode.
- `docs/examples/`: sample requests for both canonical modes.
- `package.json`: **authoring** tools and dependencies (pnpm), npm render executable, and package `files`.
- `.nvmrc` and `.editorconfig`: authoring environment configuration.
- `bin/starter-render.mjs`: generic `@mood481/starter-renderer` delegation for consumers.

Starter ID: `starter-cli-gear`
Enabled modes: `standalone, module`
Default mode: `module`
Template layout: `overlay`


The repository is created with pnpm pinned to the version bundled by the starter
CLI. Authors may update `packageManager` and `engines.pnpm` together. This pin
does **not** determine whether a project rendered from the starter uses npm or pnpm.
The project package manager belongs to its template/overlay and render inputs.

## Authoring workflow

1. Complete `starter.yaml` and its active global and mode-specific sources.
2. Keep source layout and required placeholders consistent for each enabled mode.
3. Provide concrete placeholder values in the relevant render example.
4. Install authoring dependencies and validate:

```bash
pnpm install
pnpm validate
```

### Default render example (module)

The `docs/render.yaml` request uses the configured default mode.
Prepare its module host first:

```bash
mkdir -p docs/workspace
pnpm exec starter-render --input docs/render.yaml
```

This creates `docs/workspace/apps/example`, without changing host root files.
Both canonical request examples remain under `docs/examples/`; only
enabled modes can actually be rendered.

The other two layouts are documented under `docs/examples/starter.*.yaml`.
These manifests are inactive and require their matching template/overlay
folders to be created or reorganized before switching; only the root
`starter.yaml` is active.
A module render does not
change the receiving workspace's package manager or lockfile.

For standalone consumer projects using npm and pnpm, the template may use
explicit `__PACKAGE_MANAGER__` and `__PACKAGE_MANAGER_VERSION__` placeholders,
or a pnpm base template plus an npm variant overlay. The renderer does not infer
package-manager values or create lockfiles.

## Standards and extensions

Shared development standards belong to `@mood481/dev-standards`, not this toolkit.
The starter may deliberately materialize a standards selection into its template
or give the generated project the dependency to manage its own selection.

Extension manifests are defined by the contract, but this renderer version does
not execute non-empty render-extension requests. Do not assume extensions,
workspace-root mutations, or starter-specific commands execute during rendering.
