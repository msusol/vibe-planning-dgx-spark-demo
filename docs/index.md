# Documentation Index

Navigation entry point for `vibe-planning-dgx-spark-demo` — a lightweight
"vibe planning" workflow demo using `docs/plans/`, a live `TODO.md`,
`.clinerules/`, and incremental git commits, with a GPU-enabled Docker
Compose example on NVIDIA DGX Spark.

See [`README.md`](../README.md) for the workflow overview and
[`CLAUDE.md`](../CLAUDE.md) for assistant guidance.

## Plans

Implementation plans and live task tracking (`docs/plans/`).

- [`dgx-docker-compose-gpu.md`](plans/dgx-docker-compose-gpu.md) —
  build and run a GPU-enabled container on DGX Spark using Docker
  Compose.
- `docs/plans/TODO.md` — central live checklist mirroring the plans
  above. Not yet present in this repo. The synchronization rule
  ([`02-plan-and-todo-sync.md`](../.clinerules/02-plan-and-todo-sync.md))
  creates and maintains it the first time the vibe-planning workflow
  is activated against the existing plan(s).

## Other folder roles (not yet populated)

The following canonical folders don't yet exist in this repo. Create
them on demand when their content emerges, following the linked rule:

| Folder | Purpose | Authoring rule |
| --- | --- | --- |
| `docs/specs/` | Feature / subsystem design docs | [`08-docs-specs.md`](../.clinerules/08-docs-specs.md) |
| `docs/adr/` | Architecture Decision Records | [`07-docs-adr.md`](../.clinerules/07-docs-adr.md) |
| `docs/investigate/` | Investigation logs | [`05-docs-investigate.md`](../.clinerules/05-docs-investigate.md) |
| `docs/roadmap/` | Time-based planning | (no dedicated rule yet) |
| `docs/process/` | Reusable operational guidance | [`09-docs-process.md`](../.clinerules/09-docs-process.md) |

## Conventions

The canonical folder model and classification rule are defined in
[`.clinerules/04-docs-canonical.md`](../.clinerules/04-docs-canonical.md).
When adding a new long-lived document under `docs/`, update this index
with a link.