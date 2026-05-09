# Activating Vibe Planning From an Existing `docs/plans/`

> Draft for a Medium how-to. This file is gitignored
> (`.git/info/exclude`) and never lands on the `medium/howto` branch.
> The branch contains only the artifacts the workflow produces.

A "vibe planning" workflow uses a small set of `.clinerules/` files to
coordinate three things: a plan, a live checklist, and an AI coding
assistant working off both. This article walks through *activating*
that workflow against a repo that already has a plan but no
checklist — the most realistic starting point.

## What you'll have at the end

- A `docs/plans/<feature>.md` plan (already exists).
- A `docs/plans/TODO.md` whose sections, ordering, and checkbox states
  trace 1:1 to the plan.
- A clear loop for keeping the two files aligned as work progresses.

The repository for this walkthrough lives at
`https://github.com/msusol/vibe-planning-dgx-spark-demo`. Clone it
and switch to `medium/howto`:

```zsh
git clone git@github.com:msusol/vibe-planning-dgx-spark-demo.git
cd vibe-planning-dgx-spark-demo
git checkout medium/howto
```

## How this tutorial is structured

Each step in the tutorial is one commit on the `medium/howto`
branch. You can navigate them with `git log` and check out any
intermediate state with `git checkout <hash>`. Each step's section
below names the commit hash so you can jump to the matching diff.

```zsh
git log --oneline medium/howto
```

Steps so far:

- **Step 0** — starting state. `git checkout medium/howto~1` (or any
  earlier hash) lands here: plan file present, no `TODO.md`.
- **Step 1** — activate the workflow. Commit `2013744`.

---

## Step 0 — Starting state

`git checkout medium/howto~1` lands you here. The relevant tree
looks like this:

```
.
├── .clinerules/
│   ├── 01-global.md                  # canonical docs/ folder roles
│   ├── 02-plan-and-todo-sync.md      # plan ↔ TODO sync rules
│   ├── 06-docs-plans.md              # plan authoring rules
│   └── ...                           # other rules (ADR, specs, ...)
├── docs/
│   ├── index.md                      # navigation entry point
│   └── plans/
│       └── dgx-docker-compose-gpu.md # the plan
├── CLAUDE.md                         # repo-level Claude guidance
└── README.md
```

Three things to notice before activating anything:

1. **The plan is already there.** `docs/plans/dgx-docker-compose-gpu.md`
   has a `## Tasks` section listing five concrete steps for getting a
   GPU-enabled container running on an NVIDIA DGX Spark.
2. **`docs/plans/TODO.md` does not exist yet.** That's the file the
   workflow is going to create.
3. **The rules are loaded into Claude Code automatically** because
   `~/.claude/CLAUDE.md` `@`-imports each `.clinerules/*.md` file. You
   can confirm by running `cat ~/.claude/CLAUDE.md` — there should be
   a managed block listing every rule.

The rules that govern this activation:

- **`02-plan-and-todo-sync.md`** — "If `docs/plans/` contains plan
  files and `docs/plans/TODO.md` does not exist, create it before
  beginning implementation work." Each plan gets its own named
  section. Tasks must use GitHub-style checkboxes.
- **`06-docs-plans.md`** — Defines the canonical plan structure
  (`# <Feature> Implementation Plan`, `## Goal`, `## Context`,
  `## Tasks`, `## Notes`).
- **`01-global.md`** — Establishes that `docs/` is the canonical home
  and `docs/plans/` the place for execution-oriented plans.

## Step 1 — Activate the workflow

> **Commit:** `2013744` — `docs(plans): activate vibe planning —
> generate TODO.md from existing plan`
>
> ```zsh
> git show 2013744 --stat
> ```

Open Claude Code in the repo root and ask:

```
Activate the vibe planning workflow against docs/plans/.
```

That's it. No other context. The rules tell Claude what to do.

### What Claude does, step by step

Following the rules, Claude:

1. **Locates the canonical docs root.** `01-global.md` says walk up
   from cwd until a `docs/` directory is found, then resolve folder
   roles relative to it. For this repo that's `<repo>/docs/`.
2. **Lists `docs/plans/`.** Finds `dgx-docker-compose-gpu.md` and no
   `TODO.md`.
3. **Reads the plan.** Specifically the `## Tasks` section, because
   that's the canonical source for derived checklist items per
   `02-plan-and-todo-sync.md`.
4. **Creates `docs/plans/TODO.md`.** The file gets a top-level
   `# TODO` heading, a section per plan file, and a checkbox per task
   in that plan's `## Tasks` block.
5. **Adds a `## Next steps` section.** Per `02`, this section ends
   the file and is grouped by plan.
6. **Updates `docs/index.md`.** Per `04-docs-canonical.md`, the index
   gets updated whenever a long-lived document is added under `docs/`.
   The "not yet present" placeholder becomes a real link to
   `plans/TODO.md`.

The generated `docs/plans/TODO.md`:

```markdown
# TODO

## DGX Spark Docker Compose GPU workflow

- [ ] Verify Docker, Docker Compose, and NVIDIA container runtime are available
- [ ] Create a Dockerfile for a GPU-capable test container
- [ ] Create a compose.yaml that requests GPU access
- [ ] Run docker compose up gpu-info to verify GPU visibility with nvidia-smi
- [ ] Run a GPU-backed command through Docker Compose to validate real GPU usage

## Next steps

### DGX Spark Docker Compose GPU workflow

1. Verify Docker, Docker Compose, and NVIDIA container runtime are available
```

Three details worth reading the rules to understand:

- **Section heading**: `## DGX Spark Docker Compose GPU workflow`
  matches the plan file's `# DGX Spark Docker Compose GPU workflow`
  title, downgraded one heading level. `02-plan-and-todo-sync.md`
  shows the mapping convention via a table.
- **Checkbox state**: every task starts open (`- [ ]`). Implementation
  hasn't begun. That's the next step.
- **Why only one item in `## Next steps`?** The rule frames Next steps
  as a *working priority list* ("when a next step is completed or
  deferred, remove or demote it"), not a duplicate of the full
  checklist. Listing the full backlog twice doesn't help anyone.

> **TODO for the article**: insert a screenshot of Claude Code at
> this point so readers see the actual session. Capture the prompt,
> the rule loads, and the diff.

### Step 2 (placeholder)

The next commit will demonstrate completing the first task — running
the Docker / nvidia-smi check, marking the checkbox done, and what
that does to `## Next steps`. Section will land here once committed.

---

## Going forward

Once activated, the loop is:

1. **Implement one task at a time.** Pick the top open checkbox; do
   the work.
2. **Mark it done.** Flip `- [ ]` to `- [x]` in `TODO.md`. The plan
   file stays as the source of truth for *what* — `TODO.md` tracks
   *how far*.
3. **Commit.** Each meaningful step gets its own commit so the audit
   trail mirrors the checklist.
4. **Add new tasks to the plan first.** Then re-run the sync. The
   rules favor the plan as the source of truth and treat `TODO.md`
   as a derived view.

If `TODO.md` and the plan drift far apart (lots of tasks present in
one but missing from the other, or stale checkbox states), the sister
rule `03-desync-cleanup.md` kicks in: pause, ask which side is the
source of truth, and reconcile.

> **TODO for the article**: cover the desync workflow in a follow-up
> section (or a separate article — it's substantial enough on its own).

---

## Why this works

Three small ideas, working together:

- **The plan is design intent.** It explains *what* and *why*. It
  evolves as the design clarifies.
- **The TODO is a live mirror.** Same tasks, with state. Section
  headings tie back to plan files, so the relationship is auditable.
- **The rules are explicit.** `~/.clinerules/02-plan-and-todo-sync.md`
  reads like a contract; the agent just follows it.

The whole setup is a few hundred lines of markdown. The article
should keep emphasizing that — Medium readers expect tooling-heavy
"productivity" posts, but the appeal here is the opposite: less
tooling, more conventions.

---

## Outline of remaining article sections

- [ ] Section: "Setting up the rule system from scratch" (cloning
      `~/.clinerules/`, the linker script, `~/.claude/CLAUDE.md`
      managed block).
- [ ] Section: "Day 2 maintenance — adding a second plan, what
      changes in TODO.md".
- [ ] Section: "When TODO.md and the plan drift" (preview of the
      `03-desync-cleanup` workflow).
- [ ] Section: "Adapting the rules for your team" (rename folders,
      add a custom rule, the `description` + `globs`/`paths`
      frontmatter convention).
- [ ] Pull-quote candidates and tweet-length summary.
- [ ] Cover image / hero diagram.