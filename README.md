# Vibe Planning Demo Repo

This repository demonstrates a lightweight "vibe planning" workflow using:

- `plan.md` files for implementation intent
- a root `TODO.md` for live task tracking
- `.clinerules/` to encourage synchronization between plans and TODOs
- incremental git commits to preserve an audit trail
- Docker Compose and a GPU-enabled container example on NVIDIA DGX Spark

## Workflow

1. Create or update a plan file in `plan.md` or `plans/*.md`
2. Ask your coding assistant to implement the plan
3. Let the rules ensure the root `TODO.md` reflects the plan
4. Implement one task at a time
5. Update `TODO.md` as tasks are completed
6. Commit each meaningful step to git
