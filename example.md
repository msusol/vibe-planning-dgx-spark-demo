# TODO

## DSPy Plan

### Phase 1: Minimal orchestration
- [x] DSPy signature and module (`dspy_hr_agent.py`)
- [x] JSONL export utilities (`dspy_export.py`)
- [x] Subprocess training hook (`dspy_hr_finetune.py`)
- [x] Evaluation utilities (`dspy_eval.py`)
- [x] vLLM serving scripts (`scripts/serve_vllm.sh`, `scripts/run_dspy.sh`)
- [x] GB10 OOM fix — QLoRA enabled, vLLM memory utilization capped
- [x] GB10 container runtime fix — `--runtime=nvidia` required
- [x] End-to-end pipeline validated with 5 seed examples

### Phase 2: Bootstrap examples
- [x] Configure teacher LM — base Llama 3.1 8B via vLLM (same host, port 8001)
- [x] Write `dspy_bootstrap.py` — two-stage: generate case_text then label alert_json
- [x] Validation gate — JSON parse + required keys + risk_level enum check
- [x] Auto-export accepted traces to JSONL (append-on-accept, crash-safe)
- [x] `scripts/run_bootstrap.sh` — runs bootstrap inside GB10 Docker image
- [x] **Run bootstrap** — `bash scripts/run_bootstrap.sh --target 80`
- [x] Review sample of generated examples for HR accuracy (~85% accurate; ~15% issue_type mislabeled as overtime_misclassification — acceptable noise)
- [x] Merge with seed: `cat data/dspy_seed.jsonl data/dspy_bootstrap.jsonl > data/dspy_combined.jsonl` (85 total examples)
- [x] Re-run full training loop against combined dataset (loss 0.804, token acc 84.5%, eval score 0.786 base model, 3/17 genuine issue_type failures)

### Phase 3: Iterative optimization loop
- [x] Write `dspy_reload.py` — load LoRA adapter via PEFT + transformers and eval against held-out set
- [x] Write `scripts/run_reload.sh` — runs dspy_reload.py inside GB10 container
- [x] Write `dspy_bootstrap_teacher.py` — provider-agnostic teacher bootstrap (local_vllm, deepseek, grok, openai, claude, gemini)
- [x] Write `scripts/run_bootstrap_teacher.sh` — runs teacher bootstrap inside GB10 container
- [x] Write `scripts/serve_vllm_teacher.sh` — serves Qwen2.5-32B-Instruct(-AWQ) as teacher on port 8002
- [x] Reduce `num_train_epochs` 3→1 in `configs/dspy_hr.yaml` to reduce adapter overfitting
- [x] **Run teacher bootstrap** — 300 examples, 0 rejected, 0 remapped (canonical issue_types enforced in labeler)
- [x] Label standardization — 80+ issue_type variants collapsed to 15 canonical values (`scripts/standardize_issue_types.py`)
- [x] Training format alignment — chat template + double-BOS fix (`add_special_tokens=False`) + vocabulary instruction in system prompt
- [x] LoRA rank r=16 → r=32 (alpha=64)
- [x] Retrain on 300 examples — loss 0.337 (final step), token acc 88.9%
- [x] Adapter eval (60 examples) — score 0.5945, delta +0.3181, issue_type 100% correct
- [ ] **Fix risk_level calibration** — model over-predicts "high" (41/60 pred vs 30/60 gold)
      Change `risk_cycle = ["high", "medium", "medium"]` in `build_pairs()` (~33% high)
      to better match natural compliance caseload distribution; regenerate 300 examples
- [ ] Outer loop: generate → export → train → reload → eval → keep best
- [ ] Automatic checkpoint selection across runs

## Next steps

**1. DSPy Phase 3 — risk_level calibration**: change `risk_cycle = ["high", "medium", "medium"]`
in `dspy_bootstrap_teacher.py build_pairs()`, regenerate 300 examples, retrain.
Current blocker: model over-predicts "high" (68% pred vs 50% gold); issue_type is now
100% correct. Fixing risk_level is the last remaining lever before pushing score past 0.65.

**2. Swap student model**: try `DeepSeek-R1-Distill-Llama-8B` (MIT) as student instead of
`Llama-3.1-8B-Instruct`. Two motivations:
  - **License**: Meta Llama 3 Community License has restrictions that complicate
    productionizing MineralLM (usage caps, derivative model naming rules, no sublicensing).
    MIT is clean for commercial deployment with no such constraints.
  - **Quality**: DeepSeek-R1 distillation gives stronger reasoning at the same 8B size,
    potentially improving HR compliance judgment without increasing serving cost.
Steps:
  1. `huggingface-cli download deepseek-ai/DeepSeek-R1-Distill-Llama-8B --local-dir models/DeepSeek-R1-Distill-Llama-8B`
  2. Update `configs/dspy_hr.yaml`: `base_model: /workspace/models/DeepSeek-R1-Distill-Llama-8B`
  3. Update `scripts/run_train.sh`: `--model_name /workspace/models/DeepSeek-R1-Distill-Llama-8B`
  4. Retrain and compare adapter eval score vs Llama 3.1 baseline (0.5945)