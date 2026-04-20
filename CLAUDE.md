# CLAUDE.md — verus-proof-synthesis

LLM-based automated proof synthesis for Verus (Rust formal verification). Contains two
systems (AutoVerus, VeruSAGE) and two benchmarks (Verus-Bench, VeruSAGE-Bench). Used by
Project-Metic as a formal verification research tool and benchmark infrastructure.

Paper references:
- AutoVerus: https://arxiv.org/abs/2409.13082
- VeruSAGE: https://arxiv.org/abs/2512.18436

## What This Repo Is

Research infrastructure for automated Verus proof synthesis — not a production service.
Provides benchmark evaluation harnesses and LLM-based proof synthesis pipelines.

## Key Invariants

- **sorry_count == 0 is the verification standard.** Any synthesized proof containing `assume`
  or `sorry` statements is not a valid proof by Project-Metic standards, even if Verus accepts
  it. Track sorry counts explicitly in evaluation.
- **Benchmark integrity.** The Verus-Bench (150 tasks) and VeruSAGE-Bench (849 tasks) benchmarks
  must not be modified once published. Evaluation results are only comparable if the benchmark
  is unchanged.
- **Leaderboard results require full reproducibility.** Any result submitted to the leaderboard
  must include: model name, temperature, sampling count, and the exact system prompt.

## Systems

| System | Benchmark | Task type |
|--------|-----------|-----------|
| **AutoVerus** | Verus-Bench (150 tasks) | Small algorithmic functions |
| **VeruSAGE** | VeruSAGE-Bench (849 tasks) | Code in large system projects |

## AutoVerus Phases

1. **Inference** — Generate initial proof candidates (few-shot)
2. **Refinement** — Refine promising candidates with targeted improvements
3. **Repair** — Debug and fix remaining verification errors

## Dev Commands

```bash
# Install
pip install -e ".[dev]"

# Run AutoVerus on a task
python autoverus/run.py --task tasks/verus_bench/task_001.json

# Run VeruSAGE evaluation
python verusage/eval.py --benchmark verusage_bench/ --output results/

# Run tests
pytest tests/ -v
```

## Tech Stack

- Python, Verus, LLM API (Claude / GPT-4), Rust

## Integration with Project-Metic

- Synthesized proofs can be submitted to Infera for independent verification
- VeruSAGE-Bench tasks include `verismo` and other Metic-relevant codebases
- Leaderboard: https://microsoft.github.io/verus-proof-synthesis/

## CI Conventions

- Runner: `magnon-enterprise-runners` — never `ubuntu-latest`

## What NOT to Do

- Do not count a synthesized proof as verified if it contains `assume` or `sorry`
- Do not modify the Verus-Bench or VeruSAGE-Bench task sets after publication
- Do not submit leaderboard results without full reproducibility metadata
