# Starter documentation

This directory contains starter-owned authoring instructions and example render
requests. Shared development standards belong to `@mood481/dev-standards`.

## Render examples

- `render.yaml`: runnable example for the configured default mode.
- `examples/render.example.yaml`: standalone reference request.
- `examples/render.module.example.yaml`: module reference request.

Both examples remain available for reference even if the starter enables only
one mode. Only enabled modes can be rendered. At creation, `render.yaml`
copies the example matching the configured `defaultMode`.

Request paths are relative to the YAML file: the root `docs/render.yaml`
creates `docs/rendered/` in standalone mode, or targets a module within
`docs/workspace/`. Create that workspace directory before module rendering.

## Template layouts

Dual-mode starters use the overlay layout by default: a shared `template/`
with per-mode `modes/<mode>/overlay/` directories. `--template-base common`
uses one global template; `split` generates separate `templates/` trees.
Inactive layout manifests under `docs/examples/starter.<layout>.yaml`
are reference-only and require corresponding source directories.

The generated `package.json.files` permits a basic `pnpm pack` without
publishing automation. Publishing tools may later add other source paths or
special-file entries as the starter evolves.
