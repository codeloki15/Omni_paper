# 🧪 Benchmark Results

| Experiment ID | Benchmark | Score | Baseline | Seeds | p-value |
|---------------|-----------|-------|----------|-------|---------|
| CLAIM-A-001 | 50-task completion rate (full vs static plan) | PENDING | PENDING | 3 | PENDING |
| CLAIM-B-001 | 50-task HITL comparison (tiered vs uniform vs none) | PENDING | PENDING | 3 | PENDING |
| CLAIM-C-001 | FSM vs LLM routing error rate | PENDING | PENDING | 3 | PENDING |
| CLAIM-D-001 | Chained task success (memory vs no memory) | PENDING | PENDING | 3 | PENDING |

## Experiment Setup Notes

- Backbone LLM: GPT-4o for all agents
- Task benchmark: 50 tasks in benchmarks/omni_tasks.json (to be created)
  - 25 single-capability tasks
  - 25 multi-capability chained tasks (≥2 capabilities)
  - 8 HITL-trigger tasks (intentionally ambiguous)
- Seeds: 3 per condition (different random order of tasks)
- Completion criterion: process_completion_agent called within 500 rounds
- Statistical test: paired t-test, α = 0.05
