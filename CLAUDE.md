# Claude Repo Guidance — vibe-planning-dgx-spark-demo

This repo demonstrates a lightweight "vibe planning" workflow on the
canonical `docs/` model. The `.clinerules/` track the global rule set
at `~/.clinerules/`, with one intentional divergence: `01-global.md`
omits the "Legacy plan/TODO workflow" section because this repo has
already migrated to `docs/plans/`. This file points Claude at the
project-specific entry points.

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

- Plans — `@docs/plans/`. New plans go here as `docs/plans/<feature>.md`.
- Live checklist — `@docs/plans/TODO.md`.
- README — `@README.md` (workflow overview).
- Specs (what/why) — `@docs/specs/` when introduced.
- ADRs — `@docs/adr/` when introduced.
- Investigations — `@docs/investigate/` when introduced.

`docs/specs/`, `docs/adr/`, and `docs/investigate/` do not yet exist;
create them on demand following the corresponding rule files.

## Planning behavior

- For new or simple work, create or update a minimal plan in
  `@docs/plans/<feature>.md` using:
  - `Goal`
  - `Context`
  - `Tasks`
  - optional `Notes`
- Expand the same plan file when complexity, risk, or coordination grows
  — adding sections like `Scope`, `Implementation approach`,
  `Risks and unknowns`, `Task breakdown`, `Ordered execution plan`,
  `Decision log`, `Exit criteria`.
- Keep `docs/plans/TODO.md` aligned with the plans per
  `02-plan-and-todo-sync.md` and `03-desync-cleanup.md`.