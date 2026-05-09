# Vibe Planning Demo Repo

This repository demonstrates a lightweight "vibe planning" workflow using:

- `docs/plans/*.md` files for implementation intent
- `docs/plans/TODO.md` as a live task checklist
- `.clinerules/` to encourage synchronization between plans and the TODO list
- incremental git commits to preserve an audit trail
- Docker Compose and a GPU-enabled container example on NVIDIA DGX Spark

## Workflow

1. Create or update a plan file in `docs/plans/<feature>.md`
2. Ask your coding assistant to implement the plan
3. Let the rules ensure `docs/plans/TODO.md` reflects the plan
4. Implement one task at a time
5. Update `docs/plans/TODO.md` as tasks are completed
6. Commit each meaningful step to git