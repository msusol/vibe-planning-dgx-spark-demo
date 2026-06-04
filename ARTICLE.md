# From Vibe Coding to Vibe Planning: A DGX Spark Docker Example with Markdown, Git, and Cline Rules

Most "vibe coding" sessions feel great and look terrible in hindsight. You get something working, but there's no clear story for how you got there, what you meant to do, or how to resume the work later.
I've started to think less about vibe coding and more about vibe planning.

The idea is simple:
- Put your intent into a `plan.md` file.
- Let a root TODO.md emerge as an implementation artifact.
- Use small, deliberate git commits so there's an audit trail.
- Add a thin layer of automation (rules) so the coding assistant is forced to respect the plan and TODO as it implements.

In this article I'll walk through a real example on an NVIDIA DGX Spark: building and running a GPU-enabled Docker container using Docker Compose, while keeping the plan, TODO, and commits in sync.

## How to follow along

I've published a public demo repository for this workflow.

- If you just want to see the final state, open the repo on main.
- If you want to follow along step‑by‑step, start from the first commit, which contains only:
  - a base README.md, and 
  - a.clinerules/ folder with the project rules.

From that first commit you can:
- Fork the repo.
- Open the first commit in GitHub's UI and use "Browse the repository at this point in the history" to see the exact baseline.
- Or locally: clone and then git checkout 3f19fef.

- Every section of this article corresponds to one or two subsequent commits.









```text
implement the first task in plans/dgx-docker-compose-gpu.md
```

...


```md
I have identified the first task from `plans/dgx-docker-compose-gpu.md`:

First Task: Verify Docker, Docker Compose, and NVIDIA container runtime are available

✅ Plan has been read and synchronized to `TODO.md` which I have created at the project root. All tasks from the plan are now listed as checkboxes.

To implement this first task, I will:
1.  Run verification checks for `docker`, `docker compose`, and NVIDIA container runtime
2.  Validate that the NVIDIA runtime is properly configured for Docker
3.  Check that GPU devices are visible on the host system
4.  Update the TODO.md checkbox status upon completion

Please toggle to Act mode so I can execute the verification commands.
```