# Claude Repo Guidance — vibe-planning-dgx-spark-demo

This repo demonstrates a lightweight "vibe planning" workflow on the legacy
flat-`plans/` layout. The `.clinerules/` mirror the global rule set at
`~/.clinerules/`; this file points Claude at the project-specific entry
points.

## Loaded rules

- `.clinerules/01-global.md`
- `.clinerules/02-plan-and-todo-sync.md`
- `.clinerules/03-desync-cleanup.md`
- `.clinerules/04-docs-canonical.md`
- `.clinerules/05-docs-investigate.md`
- `.clinerules/06-docs-plans.md`
- `.clinerules/07-docs-adr.md`
- `.clinerules/08-docs-specs.md`
- `.clinerules/10-commit-description.md`
- `.clinerules/11-markdown-codeblocks.md`
- `.clinerules/12-shell.md`

## Documentation layout

This project predates the canonical `docs/` model. Treat the following as
the active layout; the `docs/`-rooted folder roles in
`.clinerules/04-docs-canonical.md` apply only after a migration.

- Plans — `@plans/` (flat). New plans go here, not under `docs/plans/`.
- Live checklist — `@TODO.md` at the repo root.
- README — `@README.md` (workflow overview).

`01-global.md` "Legacy plan/TODO workflow" covers this layout: keep
`plans/` and root `TODO.md` aligned per `02-plan-and-todo-sync.md` and
`03-desync-cleanup.md`. Use the legacy paths until a deliberate migration
to `docs/plans/`.

## Planning behavior

- For new or simple work, create or update a minimal plan in
  `@plans/<feature>.md` using:
  - `Goal`
  - `Context`
  - `Tasks`
  - optional `Notes`
- Expand the same plan file when complexity, risk, or coordination grows
  — adding sections like `Scope`, `Implementation approach`,
  `Risks and unknowns`, `Task breakdown`, `Ordered execution plan`,
  `Decision log`, `Exit criteria`.
- Keep `TODO.md` aligned with `plans/` per the synchronization rules.

## Migration to docs/ (deferred)

If the project later adopts the `docs/` canonical model:

1. Create `docs/plans/`, move existing `plans/*.md` into it.
2. Move `TODO.md` to `docs/plans/TODO.md`.
3. Drop the legacy section above and let `04-docs-canonical.md` take over.
4. Update `README.md` workflow steps to reference `docs/plans/`.

Until then, follow the legacy paths exactly as documented above.