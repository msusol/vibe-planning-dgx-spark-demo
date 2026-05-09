---
description: Authoring rules for feature and subsystem specifications under docs/specs/ (minimal vs expanded, no spec-v2.md, ADR/plan extraction)
paths:
  - docs/specs/**/*.md
---

# Feature and subsystem specification rules

Treat files under `docs/specs/` as feature or subsystem specifications.

Specs capture the design intent of a feature or subsystem: what to build, why, and what the constraints are. Unlike ADRs, specs evolve as the design matures. Unlike plans, specs focus on the design rather than the execution sequence.

## When to create a spec

Create a spec when:

- A new feature or subsystem requires a coordinated design before implementation.
- An existing feature is being redesigned in a way that affects its public shape, data model, integration boundary, or user-visible behavior.
- A plan is accumulating significant "why" and "what" content that belongs in a design document, not an execution checklist.
- Multiple components or teams need to align on an interface or contract.

Do not create a spec for:

- Routine refactors or local cleanup.
- Single architectural decisions with no surrounding feature design — those go in `docs/adr/`.
- Implementation sequencing or task tracking — that goes in `docs/plans/`.

## Default (minimal) spec mode

For new or small specs, start minimal. The minimal format should usually include:

- `# <Feature or subsystem name> Specification`
- `## Summary`
- `## Goals`
- `## Design`
- `## Open questions` (optional)

Guidelines:

- Keep prose tight and decision-oriented.
- Show data shapes, interfaces, or sequence flows when they clarify the design more than prose.
- Do not introduce extra sections until they earn their place.

## When to expand a spec

Expand the spec only when one or more of the following is true:

- The feature touches multiple systems, services, or teams.
- There are meaningful tradeoffs between alternatives that future readers will need to understand.
- Risks, dependencies, or rollout concerns require explicit tracking.
- The interface or contract needs to be precisely documented for integration.
- Implementation reveals unknowns that need formal capture.
- The user explicitly asks for a more detailed spec.

## Expanded spec sections

When a spec needs more structure, evolve the same file by adding only the sections that improve clarity, such as:

- `## Goals and non-goals`
- `## Background`
- `## Requirements`
- `## Design`
  - `### Data model`
  - `### Interfaces`
  - `### Sequence` or `### Flows`
- `## Alternatives considered`
- `## Risks and unknowns`
- `## Rollout`
- `## Observability`
- `## Open questions`
- `## Decision log`
- `## References`

Rules:

- Add sections incrementally; avoid empty sections.
- Keep the document scannable; headings should reflect real content, not ceremony.
- Prefer one well-organized spec over multiple overlapping ones for the same feature.

## Editing discipline

- Specs are living documents until the feature stabilizes. Edit in place rather than forking versions.
- When a structural decision in the spec is worth preserving across future redesigns (because its rationale will outlive this feature), extract it into an ADR and reference the ADR from the spec.
- When the spec accumulates execution-level detail (sequencing, task ownership, deadlines), move that content to a corresponding `docs/plans/` document and reference it from the spec.
- Once a feature has shipped and stabilized, prefer updating the spec to reflect "as-built" reality rather than leaving it frozen at the design moment. If "as-designed" history matters, capture that in `## Decision log` or in linked ADRs.
- Avoid creating `spec-v2.md`, `spec-new.md`, or similar parallel files. If a redesign is large enough to warrant its own document, write a new spec with a distinct scope and link the two.

## Relationship to other docs

- Use `docs/adr/` for single architectural decisions captured as immutable records. Specs may reference ADRs.
- Use `docs/plans/` for sequencing the implementation. Plans may reference specs but should not duplicate design content.
- Use `docs/investigate/` when behavior is unclear or being debugged. Promote stabilized findings into specs or ADRs once decisions are made.
- Use `docs/index.md` to link to specs alongside other canonical docs.

If a spec begins to read like a sequence of tasks, move that content into a plan. If a spec begins to read like a single decision with consequences, extract that decision into an ADR.