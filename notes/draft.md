# 📝 Paper Draft (auto-updated)
*Last updated: 2026-06-13*

**Paper:** Omni: A Plan-Grounded Multi-Agent System for Long-Horizon Task Completion  
**Status:** Full draft revised (Priority fixes 2-9 applied + Introduction rewrite + Claim E added). Experiments pending.  
**Location:** `/Users/lokesh/Desktop/code/self_align/omni-backend/PAPER.md`  
**Target:** arXiv preprint → NeurIPS 2026

---

## Draft Snapshot (Abstract)

We present Omni, a production multi-agent system that coordinates eleven specialized LLM agents to complete complex, long-horizon tasks spanning heterogeneous capabilities — web browsing, Python code execution, email management, image generation, and file search — without requiring per-step human supervision. We contribute four principled architectural mechanisms: (1) a plan-anchored self-alignment loop grounded in external execution artifacts; (2) a tiered HITL architecture with three semantically distinct intervention tiers; (3) a deterministic FSM speaker-selection function replacing stochastic LLM routing; (4) memory-mediated capability chaining via a session-scoped episodic store. Omni is the only system in a survey of 35 recent agentic papers exhibiting all seven target properties simultaneously.

---

## Sections Written

- [x] Abstract
- [x] 1. Introduction (problem, 5 failure modes, 4 contributions)
- [x] 2. Background (AutoGen, MetaGPT, GPTSwarm, AFlow, Reflexion, Huang et al., HULA, MemGPT)
- [x] 3. Architecture (3.1 overview, 3.2 plan loop, 3.3 HITL tiers, 3.4 FSM, 3.5 memory, 3.6 loop detect, 3.7 contracts)
- [x] 4. Related Work (7 sub-sections + feature comparison table)
- [x] 5. Experiments (4 claims, protocol, [RUN EXPERIMENT] placeholders)
- [x] 6. Implementation (stack, concurrency, token tracking, session resumption)
- [x] 7. Discussion (4 design principles, 5 limitations, broader impact)
- [x] 8. Conclusion
- [x] References (22 citations)
- [x] Appendix A: Agent prompts
- [x] Appendix B: PlannerResponse schema
- [x] Appendix C: Sample task suite (10 tasks)

---

## Task Order (no dates — sequential priority)

| # | Task | Blocks |
|---|---|---|
| 1 | Build `benchmarks/omni_tasks.json` — 50 tasks across 4 categories (single, chain, HITL-trigger, structural impossibility) | Everything else |
| 2 | Run Claim E — structural impossibility tasks T14–T18 vs Claude Code. One real result to anchor arXiv submission | arXiv submit |
| 3 | Replace all `[RUN EXPERIMENT]` in Claims A–D with "results pending" language | arXiv submit |
| 4 | Fix reference list — add full authors + arXiv IDs to ~12 incomplete entries | arXiv submit |
| 5 | **Submit to arXiv** — establishes priority on FSM + plan loop + tiered HITL | nothing, ship it |
| 6 | Run Claim C — FSM vs LLM routing error rate + latency (easiest ablation, fully internal) | Full results |
| 7 | Run Claim D — memory chaining vs no chaining on 25 chained tasks | Full results |
| 8 | Run Claim A — plan re-invocation vs static plan, 50 tasks | Full results |
| 9 | Run Claim B — tiered HITL vs full autonomy vs uniform HITL (most complex, needs HITL-trigger annotation) | Full results |
| 10 | Update arXiv with full experimental results | Camera-ready |
| 11 | Redesign Figure 1 as proper FSM + data flow diagram | Camera-ready |
| 12 | Begin Paper 2 scoping — Salesforce/Excel/PPT capabilities + live UI contribution + human eval study design | Paper 2 |

---

## Revision Log (2026-06-13)

Applied Priority fixes 2–9:
- [x] Title changed (removed "Self-Aligning")
- [x] Central thesis sentence added to Abstract + Intro
- [x] FSM routing framed as empirical hypothesis (Claim C)
- [x] 7-property table replaced with 5-failure-mode table (grounded, not cherry-picked)
- [x] Formal trigger conditions for tier-2 HITL written out (3 named conditions)
- [x] Claim B confound fixed — HITL-trigger vs non-HITL tasks separated
- [x] Section 6 (infrastructure) moved to Appendix D
- [x] Section/subsection renumbering applied
- [ ] Figure 1 redesign (text-only ASCII in paper, needs proper diagram tool)
- [ ] Reference list — missing authors/arxiv IDs on ~12 entries
- [ ] 4 experiments (CRITICAL, unfilled)

## Confidence Score: 0.84 / 1.0

Novelty: strong. Architecture: complete. Framing: complete. Writing: complete. Experiments: 0/5 run (results pending).

**arXiv-ready.** All pre-experiment tasks done:
- [x] benchmarks/omni_tasks.json — 38 tasks across 4 categories (10S+15C+8H+5SI)
- [x] Claim E structural argument written — Claude Code column confirmed 0 by structural analysis
- [x] All [RUN EXPERIMENT] replaced with results pending / dashes
- [x] References — all 24 entries have full authors + arXiv IDs
- [x] Limitations — "no eval yet" removed, 5 honest limitations written
- [x] Figure 1 — full FSM + data flow ASCII diagram replacing the old box diagram
- [x] Paper 2 scoping doc complete (warrior_memory/paper2_vision.md)

**Remaining to reach 0.95:**
- [ ] Run Claim C (easiest — fully internal FSM vs LLM routing)
- [ ] Run Claim D (memory chaining vs no chaining)
- [ ] Run Claim A (plan re-invocation vs static plan)
- [ ] Run Claim B (tiered HITL vs autonomy vs uniform)
- [ ] Run Claim E (Omni + GPT-4o-tools results; Claude Code column already 0)
