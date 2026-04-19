---
paths:
  - "plan.md"
  - "plans/**/*.md"
  - "TODO.md"
---

# Plan implementation and TODO.md synchronization

- Always locate the root-level `TODO.md` before making implementation changes.
- If `TODO.md` does not exist, create it at the project root with the heading `# TODO`.
- All tasks in `TODO.md` must use GitHub-style checkboxes:
  - Open task: `- [ ] Task description`
  - Completed task: `- [x] Task description`

## Deriving tasks from plans

- Treat `plan.md` and `plans/**/*.md` as structured sources of tasks.
- Scan plan files for bullet lists and concrete section headings.
- For each plan task, ensure there is a corresponding checkbox entry in `TODO.md`.
- Keep TODO text concise but traceable to the plan.
