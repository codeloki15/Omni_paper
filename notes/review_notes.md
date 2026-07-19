# 📋 Self-Review Notes — Adversarial Critique Pass (June 2026)

| Date | Section | Comment | Severity | Status |
|------|---------|---------|----------|--------|
| 2026-06-13 | ALL | No experiments — paper cannot be submitted to any venue in this state | critical | OPEN |
| 2026-06-13 | Title | "Self-Aligning" is overclaimed — the system does not align itself, it updates a plan | high | FIXED — new title "A Plan-Grounded Multi-Agent System..." |
| 2026-06-13 | Abstract | Claims "only system" with all 7 properties — this is a self-defined checklist, reviewers will call it cherry-picked | high | FIXED — 7-property table replaced with 5-failure-mode table grounded in failure modes |
| 2026-06-13 | Contribution 3 | FSM routing novelty is weak — this is standard workflow engine design, not ML research | high | FIXED — FSM framed as empirical hypothesis (Claim C), zero-error rate by construction is the claim |
| 2026-06-13 | Contribution 1 | Plan loop framed as "self-alignment" but Huang et al. critique applies differently — needs sharper framing | high | FIXED — renamed "plan-anchored correction loop", thesis sentence added |
| 2026-06-13 | Section 3 | Architecture section reads like a system manual, not a research paper — missing "why this design over alternatives" | high | PARTIAL — motivation paras improved; full "alternatives" framing needs work |
| 2026-06-13 | Section 4 | Related work is thorough but defensive — reads like "we beat everyone" rather than "we contribute X to the field" | medium | PARTIAL — table reframed around failure modes |
| 2026-06-13 | Section 5 | Experiment designs are good but Claim C (FSM routing error = 0 by construction) is not a real experiment | medium | FIXED — Claim C restated: interesting question is LLM routing error rate, not FSM rate |
| 2026-06-13 | Section 5 | Claim B confound — full autonomy vs uniform HITL conflates success mechanism | medium | FIXED — HITL-trigger tasks vs non-HITL tasks separated |
| 2026-06-13 | Section 6 | Infrastructure section (WebSocket, MongoDB, token tracking) belongs in an appendix, not the main paper | medium | FIXED — moved to Appendix D |
| 2026-06-13 | Framing | No central research question — paper lists 4 contributions without a unifying thesis | medium | FIXED — central thesis sentence added to abstract and intro |
| 2026-06-13 | HITL tiers | Tier-2 trigger conditions not formally specified | medium | FIXED — 3 named conditions written out |
| 2026-06-13 | Novelty | "Pydantic contracts" framed as a contribution is engineering hygiene, not a research contribution | medium | OPEN — still listed as a supplementary mechanism, not a contribution; acceptable |
| 2026-06-13 | Limitations | Limitation "no eval yet" should not be in the paper — fix it before submitting | medium | OPEN — still present, must be removed before camera-ready |
| 2026-06-13 | References | ~12 entries missing full author names / arxiv IDs | medium | OPEN |
