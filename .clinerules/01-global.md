# Global project workflow

## Locating the canonical docs root

Before doing significant work, find the **nearest** `docs/` root using this resolution order:

1. Check the directory containing the file being edited (or the current working directory).
2. Walk up toward the repo root, checking each ancestor directory for a `docs/` subdirectory.
3. Stop at the first directory that contains `docs/` — that is the **docs root** for this context.
4. If no `docs/` is found anywhere, create one at the repo root.

Use `docs/index.md` under the resolved docs root as the primary entry point when it exists.

This allows subdirectories that are independent repos (or sub-projects) to maintain their own `docs/` without being overridden by a parent `docs/`.

- Treat the resolved `docs/` as the canonical home for long-lived project documentation.
- Treat `docs/specs/` as the place for feature and subsystem design docs (what to build and why).
- Treat `docs/plans/` as the place for feature-level implementation plans and task-level sequencing (how to execute).
- Treat `docs/roadmap/` as the place for time-based planning and initiative sequencing.
- Treat `docs/investigate/` as the place for investigation logs and issue analysis.
- Treat `docs/process/` as the place for reusable workflow and operational guidance.

## Legacy plan/TODO workflow

This section is a conditional fallback. It applies **only** when at least one of the following legacy artifacts exists in the repository:

- a root-level `TODO.md`, `plan.md`, or `plans.md`
- a top-level `plans/` directory (i.e. `<repo>/plans/`, not `docs/plans/`)

If none of these are present, the legacy fallback does not apply: `docs/plans/` is canonical with no further migration step, and the rest of this rule (plus `02-plan-and-todo-sync.md` and `03-desync-cleanup.md`) operates against `docs/plans/` directly.

When at least one legacy artifact is present:

- Treat the legacy files as transitional. The canonical location for `TODO.md` remains `docs/plans/TODO.md`.
- Keep the legacy files aligned with `docs/plans/` according to:
  - `.clinerules/02-plan-and-todo-sync.md`
  - `.clinerules/03-desync-cleanup.md`
- Create new long-lived planning artifacts in `docs/plans/` rather than in the legacy top-level `plan*` files or `plans/`.
- When migrating, `git mv` the legacy files into `docs/plans/` (and remove the now-empty `plans/` directory). After the migration, this section no longer applies to the repository.
