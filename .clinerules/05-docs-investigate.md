---
paths:
  - docs/investigate/**/*.md
---

# Investigation document rules

- Treat files under `docs/investigate/` as analyst-mode documents, not specs or ADRs.
- A single investigation file may contain one issue or many issues.
- Treat each level-2 heading (`##`) as a separate issue entry.
- Preserve user-authored issue headings and initial freeform context.

## Per-issue structure

When actively working an issue, add these subsections if they are missing:

- `### Context`
- `### Investigation Checklist`
- `### Findings`
- `### Actions Taken`
- `### Resolution`
- `### Follow-ups`

## Section meanings

### Findings

`### Findings` must contain:

- observations
- evidence
- confirmed causes
- explicitly marked hypotheses
- conclusions drawn from investigation

Do not record changes, edits, mitigations, or commands here.

### Actions Taken

`### Actions Taken` must contain:

- code changes
- config changes
- commands run
- mitigations applied
- rollbacks performed
- verification steps executed

Do not restate findings here.

### Resolution

Always add or update `### Resolution` after meaningful progress on an issue.

`### Resolution` must include one of these statuses:

- `resolved`
- `partially resolved`
- `unresolved`
- `deferred`
- `not reproducible`

Add a brief statement describing the current outcome and, when applicable, how it was verified.

### Follow-ups

Use `### Follow-ups` for:

- remaining risks
- open questions
- deferred work
- additional validation needed
- next steps

## Editing discipline

- Only modify issue entries actively being worked on.
- Avoid unnecessary rewrites of untouched issue entries.
- Preserve numbered issue headings such as `## 1. Some issue found` when they already exist.
