# Omni vs. 35 Agentic AI Papers: Literature Comparison

> **Research sweep:** 63 papers found · 35 compared · 66 subagents · June 2026  
> **Venues covered:** NeurIPS, ICLR, ICML, ACL, arXiv 2023–2025

---

## Feature Comparison Table

| Paper | Year | Multi-Agent | Self-Alignment | HITL | FSM Routing | Memory Chaining | Tool Use | Long-Horizon |
|---|---|---|---|---|---|---|---|---|
| **Omni** (this work) | 2025 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Magentic-UI | 2025 | ✓ | ~ | ✓ | ~ | ~ | ✓ | ~ |
| Puppeteer (Evolving Orchestration) | 2025 | ✓ | ~ | ✗ | ~ | ✗ | ~ | ~ |
| Multi-Agent Collaboration Survey | 2025 | ✓ | ~ | ~ | ✗ | ✗ | ~ | ~ |
| AgentRefine | 2025 | ~ | ~ | ✗ | ✗ | ~ | ~ | ~ |
| ReflectEvo | 2025 | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ~ |
| AgentOrchestra | 2025 | ✓ | ~ | ✗ | ✗ | ~ | ✓ | ✓ |
| CowPilot | 2025 | ~ | ✗ | ~ | ✗ | ✗ | ~ | ✗ |
| ARIA (Self-Improving HITL) | 2025 | ~ | ~ | ~ | ✗ | ✗ | ~ | ~ |
| Plan-and-Act | 2025 | ~ | ✗ | ✗ | ✗ | ✗ | ~ | ✓ |
| DeepSeek-R1 | 2025 | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ~ |
| SCoRe (Self-Correction via RL) | 2024 | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ |
| LLM-HAS Survey | 2025 | ~ | ✗ | ✓ | ✗ | ✗ | ~ | ~ |
| PwS (Cognitive Bandwidth) | 2025 | ~ | ~ | ✗ | ✗ | ✗ | ~ | ~ |
| AFlow | 2024 | ✓ | ~ | ✗ | ~ | ✗ | ~ | ~ |
| AutoGen | 2024 | ✓ | ✗ | ~ | ✗ | ✗ | ~ | ~ |
| MetaGPT | 2024 | ✓ | ✗ | ✗ | ✗ | ✗ | ~ | ~ |
| GPTSwarm | 2024 | ✓ | ✗ | ✗ | ✗ | ✗ | ~ | ~ |
| HULA | 2024 | ~ | ✗ | ~ | ✗ | ✗ | ~ | ~ |
| LLM Self-Critique (planning) | 2024 | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ~ |
| Meta-Rewarding LMs | 2024 | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ |
| LLMs Cannot Self-Correct (Huang) | 2024 | ✗ | ~ | ✗ | ✗ | ✗ | ✗ | ✗ |
| Self-Reflection in LLM Agents | 2024 | ~ | ~ | ✗ | ✗ | ✗ | ✗ | ✗ |
| SER (Self-Evolved Reward) | 2024 | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ |
| TravelPlanner | 2024 | ~ | ✗ | ✗ | ✗ | ✗ | ~ | ✓ |
| ST-WebAgentBench | 2024 | ~ | ✗ | ~ | ✗ | ✗ | ~ | ~ |
| OSWorld | 2024 | ✗ | ✗ | ~ | ✗ | ✗ | ✓ | ✓ |
| SWE-agent | 2024 | ✗ | ~ | ✗ | ✗ | ✗ | ✓ | ~ |
| HiAgent | 2024 | ✗ | ~ | ✗ | ✗ | ~ | ~ | ✓ |
| ReConcile | 2024 | ✓ | ~ | ✗ | ✗ | ✗ | ✗ | ✗ |
| MLDT | 2024 | ~ | ✗ | ✗ | ✗ | ✗ | ~ | ✓ |
| LaMMA-P | 2024 | ✓ | ✗ | ✗ | ✗ | ✗ | ~ | ✓ |
| ReAcTree | 2024 | ✓ | ✗ | ✗ | ✗ | ✗ | ~ | ✓ |
| EM-LLM (Episodic Memory) | 2024 | ✗ | ✗ | ✗ | ✗ | ~ | ✗ | ~ |
| Reflexion | 2023 | ✗ | ✓ | ✗ | ✗ | ~ | ~ | ~ |
| MemGPT | 2023 | ✗ | ✗ | ✗ | ✗ | ✓ | ~ | ✓ |

**Column key:** ✓ fully present · ~ partial or limited · ✗ absent  
**Omni is the only system in this survey with all 7 properties simultaneously.**

---

## Related Work (Full Academic Section)

### Multi-Agent Orchestration Frameworks

Omni builds directly on AutoGen [AutoGen, COLM 2024], which defines GroupChat-based multi-agent conversation with customizable speaker selection and human-input modes. We adopt AutoGen's GroupChat primitive as our substrate while identifying three limitations in production deployments: LLM-based speaker selection is nondeterministic and costly per turn; GroupChat instances are memory-isolated; and free-text inter-agent messages permit silent schema failures.

MetaGPT [MetaGPT, ICLR 2024 Oral] addresses coordination reliability in software pipelines by encoding human SOPs as structured prompt sequences with typed artifact outputs, demonstrating that structured inter-agent communication reduces cascading hallucinations. Omni extends this insight beyond single-domain assembly-line pipelines: our deterministic FSM speaker selection (`custom_speaker_selection_func`) eliminates all LLM calls from the routing critical path, while Pydantic-enforced output contracts generalize MetaGPT's artifact typing across five heterogeneous capability modalities.

GPTSwarm [GPTSwarm, ICML 2024] formalizes agents as optimizable DAGs and uses REINFORCE to discover high-performing topologies; AFlow [AFlow, ICLR 2025] automates workflow graph discovery via MCTS. Both approaches optimize topology offline. Omni instead encodes topology as a verified, auditable FSM — sacrificing adaptability for zero-latency routing decisions and full reproducibility — and directs design effort toward mechanisms these frameworks leave open: plan-anchored replanning, tiered HITL, and episodic memory propagation.

AgentOrchestra [AgentOrchestra, arXiv 2025] introduces hierarchical planner-executor decomposition with dependency-aware parallel dispatch and approaches 89% on GAIA via dynamic tool synthesis. Omni shares the hierarchical two-level structure (master GroupChat over capability sub-GroupChats) but contributes plan-anchored continuous replanning, three-tier HITL with async timeout semantics, and loop detection in code agents — mechanisms absent from AgentOrchestra's fully autonomous design.

The multi-agent collaboration survey [Survey MAC, arXiv 2025] provides a five-dimensional taxonomy (actors, collaboration type, organizational structure, coordination strategy, communication protocols) under which Omni occupies a centralized-structure, role-based, hybrid-coordination position; we operationalize the survey's implicit static-versus-dynamic routing tradeoff and introduce HITL and plan-alignment dimensions the taxonomy cannot currently represent.

### Self-Alignment and Reflection in Agents

Reflexion [Reflexion, NeurIPS 2023] established that verbal self-critique stored in episodic memory enables agent self-improvement without gradient updates, though within single-task retry loops over at most five trials. Huang et al. [LLMs Cannot Self-Correct, ICLR 2024] demonstrated that intrinsic self-correction without external ground-truth signals reliably degrades accuracy, drawing a sharp distinction between oracle-guided and autonomous correction. Omni takes this finding as a design axiom: the plan-anchored self-alignment loop grounds every correction in concrete external execution results injected via OmniMemory rather than in the model's self-belief — structurally equivalent to the oracle condition Huang et al. identify as necessary.

SCoRe [SCoRe, arXiv 2024] bakes self-correction into model weights via two-stage multi-turn RL on self-generated data; Meta-Rewarding [Meta-Rewarding, arXiv 2024] bootstraps alignment through LLM-as-meta-judge DPO iterations. Omni addresses the orthogonal problem of inference-time orchestration-level self-alignment: the plan-anchored loop achieves continuous task-status correction on any frozen LLM without gradient updates, making architectural self-alignment and training-time self-correction complementary rather than competing contributions.

Plan-and-Act [Plan-and-Act, arXiv 2025] fine-tunes separate Planner and Executor models and achieves strong WebArena results, but generates the plan once and executes open-loop. Omni re-invokes the planner after every capability return to reconcile plan state with observed outcomes — continuous plan refinement that fine-tuned static planners cannot provide.

### Human-in-the-Loop Agent Systems

HULA [HULA, arXiv 2024] is among the first industrial-scale HITL deployments, gating file-localization and coding plans at two fixed pipeline stages before code generation. Omni generalizes the HITL architecture to three semantically distinct tiers — plan confirmation (`get_user_acceptance`), mid-task ambiguity resolution (`manual_verification`), and terminal free-form input (`user_input`) — each with a 600-second async timeout and dynamic triggering, addressing HULA's identified open challenges of trust calibration and plan rigidity.

Magentic-UI [Magentic-UI, arXiv 2025] is the closest architectural peer, implementing six HITL interaction modes including action guards and co-planning on top of Magentic-One, with WebSocket streaming and ChromaDB cross-session memory. Omni differentiates on three axes: deterministic FSM speaker selection eliminates a per-turn LLM routing call required by Magentic-UI's Orchestrator; Pydantic-enforced inter-agent schemas make communication failures structurally detectable; and the plan-anchored replanning loop continuously revises task state rather than requiring user-initiated plan edits.

CowPilot [CowPilot, arXiv 2025] empirically quantifies that 15.2% human effort yields 95% task accuracy in web navigation, validating the efficiency of targeted intervention; Omni's three-tier architecture addresses a limitation CowPilot reveals — its single-tier pause-resume gesture cannot differentiate strategic plan-level from tactical action-level intervention.

ARIA [ARIA, arXiv 2025] demonstrates proactive uncertainty-driven HITL elicitation for persistent knowledge adaptation in a production compliance setting; Omni targets the complementary orchestration axis, embedding structured intervention tiers within a multi-capability execution pipeline rather than accumulating cross-session domain knowledge.

The LLM-HAS survey [LLM-HAS Survey, arXiv 2025] taxonomizes human-agent systems by feedback type and human role (supervisor, collaborator, delegator); Omni simultaneously instantiates all three roles within one FSM-routed, episodic-memory-backed runtime — providing the first production-system instantiation of this combined taxonomy.

### Long-Horizon Task Planning

TravelPlanner [TravelPlanner, ICML 2024] establishes that even GPT-4 achieves 0.6% end-to-end success on constraint-rich multi-step tasks, attributing failures to constraint forgetting, tool misuse, and multi-step state loss — the precise failure modes Omni's plan-anchored loop, structured output contracts, and deterministic FSM routing are designed to prevent architecturally.

OSWorld [OSWorld, NeurIPS 2024] constructs a computer-task benchmark revealing that agents fail dramatically on multi-application long-horizon workflows; Omni addresses the coordination and planning mechanisms such agents require for programmatic (non-GUI) tool use.

HiAgent [HiAgent, arXiv 2024] organizes in-trial working memory hierarchically around subgoals, achieving 2x success-rate improvements with 35% fewer tokens on sequential planning benchmarks; Omni extends beyond HiAgent's single-environment, single-agent scope to coordinate 11 specialized agents via deterministic FSM across heterogeneous real-world capabilities with explicit HITL escalation.

ReAcTree [ReAcTree, arXiv 2024] constructs dynamic hierarchical agent trees with sequential, conditional, and parallel control-flow nodes, nearly doubling flat ReAct success rates. Omni shares runtime parallel dispatch but contributes typed inter-agent contracts, continuous plan re-invocation, and production HITL tiers that ReAcTree's simulated-environment focus does not address.

### Agent Memory Systems

MemGPT [MemGPT, arXiv 2023] introduced OS-inspired hierarchical memory (main context, recall, archival storage) for extending a single LLM agent's effective context window via self-directed memory tool calls. OmniMemory in Omni solves a structurally distinct problem: propagating episodic context across multiple structurally isolated GroupChat instances within a single task execution pipeline, using application-managed deterministic injection (`context_keeper` messages) rather than LLM-self-directed memory operations.

EM-LLM [EM-LLM, arXiv 2024] extends single-model context to effectively infinite length via Bayesian surprise-based event segmentation and two-stage semantic-plus-temporal retrieval. Omni's OmniMemory retrieval is recency-based by deliberate design — prioritizing determinism and auditability over recall precision — while EM-LLM-style semantic retrieval is a natural future extension. Together, MemGPT and EM-LLM address intra-agent long-context continuity; Omni addresses inter-agent episodic handoff, a problem that does not arise in single-agent settings.

### Tool Use and Capability Routing

SWE-agent [SWE-agent, NeurIPS 2024] established that purpose-built Agent-Computer Interfaces, rather than raw tool access, are the primary driver of multi-step coding task performance, and incorporates linting feedback to partially mitigate unproductive code-generation cycles. Omni's `python_developer` capability addresses the same loop-breaking problem with a capability-agnostic `LoopDetector` sliding window, and generalizes beyond code to four additional capability modalities.

Puppeteer [Puppeteer, NeurIPS 2025] learns agent invocation order via REINFORCE with a cost-penalizing reward, inducing efficient hub-dense and cyclical routing topologies. Omni makes the opposite design choice: a fixed, deterministic FSM sacrifices learned adaptability for zero routing-inference cost and full auditability — properties required in regulated deployments where every agent transition must be traceable to a specific Python predicate.

The Cognitive Bandwidth Perspective [PwS, arXiv 2025] diagnoses how growing action-space size exhausts LLM planning capacity and proposes schema-level abstractions to reduce decision-time enumeration burden. Omni addresses the complementary orchestration layer: rather than abstracting action representations within a single agent turn, we route across 11 specialized agents via FSM, deferring enumeration to targeted LLM calls only at dependency-detection and capability-dispatch steps.

### Agent Reliability and Safety

ST-WebAgentBench [ST-WebAgentBench, arXiv 2024] demonstrates that top agents achieve only ~66% policy-compliant task completion (CuP metric), exposing a systematic gap between surface-level task success and safe task success. Omni converts safety from a measurable post-hoc property into a structural pre-condition: the three-tier HITL architecture with 600-second async gates enforces human confirmation before irreversible actions, and the deterministic FSM guarantees that no agent transition can be reached outside the hand-coded policy.

AgentRefine [AgentRefine, arXiv 2025] addresses a complementary layer — training self-correction meta-skills into model weights via refinement tuning — demonstrating that architectural scaffolding and model-level correction are orthogonal: a refinement-tuned backbone could be substituted into Omni's planner or router agents, potentially improving both systems.

---

## Novelty Analysis

### 1. Genuine Novelty Gaps — Claims No Paper in This Survey Makes

**Gap 1: Three-Tier HITL with Typed Timeout Contracts as a First-Class Architectural Primitive**

No paper in this survey implements, formalizes, or proposes a structurally differentiated three-tier HITL model where each tier maps to a distinct agent node (`get_user_acceptance`, `manual_verification`, `user_input`), has a defined semantic trigger condition, and carries a quantified timeout (600s) with async fallback.

- HULA: two fixed pipeline stages, no timeout model
- Magentic-UI: unifies co-planning and co-tasking without distinguishing interrupt semantics
- CowPilot: collapses all intervention into a single pause gesture
- AutoGen: uses synchronous stdin
- None treat the three tiers as semantically distinct primitives with formal trigger conditions

**Gap 2: Deterministic Zero-Cost FSM Speaker Selection as an Architectural Safety Mechanism**

While AutoGen, MetaGPT, GPTSwarm, and Puppeteer all use LLM calls or learned policies for routing, Omni's hand-coded Python FSM is the only system in this survey where routing is provably auditable, formally verifiable (reachable states can be statically enumerated), and zero-cost per transition. No paper frames deterministic FSM routing as a *safety and auditability* mechanism rather than merely an efficiency choice.

**Gap 3: Plan-Anchored Self-Alignment as a Continuous Mid-Execution Ground Truth**

The `planner_agent` being re-invoked after every capability return — reading actual execution artifacts and updating a Pydantic `PlannerResponse` in-place — is not proposed in any surveyed paper:
- Plan-and-Act: generates plans once, no mid-execution revision
- HiAgent: compacts memory but does not re-plan
- ReAcTree: decomposes at tree-construction time only
- AFlow: graph is static during execution

The pattern of "execution outcome → replanning → next execution" as a closed loop across heterogeneous capabilities is unrepresented in this survey.

**Gap 4: Memory-Mediated Cross-Capability Context Injection via `context_keeper` Messages**

OmniMemory's specific pattern — extracting outputs from one capability's GroupChat, storing them indexed by `(chat_id, capability_name, sequence_id)`, then injecting them as synthetic `context_keeper` agent messages into a structurally separate capability's GroupChat — is not described by any surveyed paper. MemGPT, HiAgent, and EM-LLM all address intra-agent long-context. No paper solves cross-GroupChat episodic handoff.

**Gap 5: Structured Pydantic Output Contracts as a Silent-Failure Prevention Mechanism**

No paper treats inter-agent schema enforcement as a first-class reliability contribution. AutoGen passes free-form strings. MetaGPT uses role-filtered pub-sub with no validation. All self-reflection papers (Reflexion, AgentRefine, ReflectEvo) use natural language inter-agent signals. Omni is the only system that enforces typed Pydantic models with enum constraints and field validators at every agent boundary, framing this as converting silent hallucination propagation into catchable validation exceptions.

**Gap 6: Pre-Task Intent Triage Before Engaging the Planning Pipeline**

No surveyed paper includes a pre-classification step (`judge_question`) that separates conversational queries from task-oriented requests before routing to a full planning pipeline. AgentOrchestra, Magentic-UI, AutoGen, MetaGPT all route all input through their full orchestration stack. This is a cost-reducing architectural primitive absent from all surveyed work.

**Gap 7: Runtime LLM-Based Dependency Detection for Capability Parallelism**

While AFlow and ReAcTree encode parallelism statically in workflow graphs, Omni's `agent_router` uses LLM inference at runtime to classify each specific query's sub-tasks as sequential or parallel via the `has_dependencies` boolean. No surveyed paper performs query-specific, LLM-driven dependency analysis for parallel dispatch at execution time.

---

### 2. Partially Novel — RAVIAN Advances Beyond Existing Work

| Claim | What's novel | What's already there |
|---|---|---|
| Loop Detection (`LoopDetector`) | Concrete sliding-window implementation in infra | Reflexion caps trials; MetaGPT has 3-retry cap |
| Two-level GroupChat hierarchy | Cross-boundary memory injection pattern | ReAcTree also uses hierarchical decomposition |
| Per-agent token delta tracking + WebSocket streaming | Full production observability stack | Systems engineering, not science |
| Hybrid FSM + LLM routing (different decisions) | Design principle operationalized in code | Survey taxonomy identifies it as possible but no system demonstrates it |

---

### 3. Not Novel — Already Well-Covered

| Claim | Why it's not novel | What to do |
|---|---|---|
| Multi-agent orchestration with specialized roles | Standard paradigm: MetaGPT, AutoGen, AgentOrchestra | Don't claim "11 agents" as a contribution |
| Episodic memory for long-context agents | MemGPT (2023), HiAgent, EM-LLM all cover this | Scope claim to *cross-GroupChat injection* specifically |
| Self-correction / plan update loops | Reflexion, SCoRe, AgentRefine, AFlow | Frame as "external-artifact-grounded replanning", not "self-correction" |
| Structured JSON outputs from LLMs | Standard practice since GPT-4 | Scope to "silent failure prevention at inter-agent boundaries" |

---

### 4. Strongest Reviewer Objections — and How to Preempt Them

**Objection 1: "No quantitative evaluation. Every claim is architectural."**

This is the most lethal objection. Every paper in this survey with empirical results will be cited against Omni.

*Preemption:* Run at minimum before submission:
- (a) 50-task ablation: full RAVIAN vs. RAVIAN without plan re-invocation → task completion rate
- (b) FSM routing vs. LLM-based routing → routing error rate + latency per turn
- (c) Tiered HITL vs. no-HITL → task success on ambiguous queries
- Target GAIA sub-tasks, WebArena, or a custom 3-capability benchmark

**Objection 2: "This is an engineering system paper, not a research contribution."**

Reviewers at theory venues will argue FSM routing, Pydantic contracts, and WebSocket infrastructure are software engineering.

*Preemption:* Frame as empirical validation of four co-dependent design principles:
1. Does deterministic routing reduce failure rate vs. LLM routing?
2. Does plan re-invocation reduce task drift on long-horizon tasks?
3. Does typed schema enforcement reduce error propagation vs. free-form?
4. Does tiered HITL reduce unnecessary interruptions vs. binary HITL?

Lead with the **principles**, not the architecture. The system is the test bed.

**Objection 3: "The HITL contribution is obvious."**

*Preemption:* The contribution is not that HITL exists. It is the formal taxonomy of three semantically distinct interrupt types with specific trigger conditions and timeout/fallback semantics. The claim is that conflating these three types (as all prior systems do) produces worse user experience than separating them.

**Objection 4: "AutoGen + Pydantic + a state machine is just AutoGen with glue code."**

*Preemption:* The response is that Omni demonstrates a set of design patterns that AutoGen's framework does not provide and that other AutoGen users have not published: specifically, the cross-GroupChat memory injection pattern, FSM replacing AutoGen's default LLM-based speaker selection, and three-tier HITL as named agent nodes. These are generalizable patterns, not one-off hacks. Consider contributing them as a reusable library.

**Objection 5: "AgentOrchestra already achieves 89% on GAIA. Why does Omni matter?"**

*Preemption:* Omni and AgentOrchestra solve different problems. AgentOrchestra optimizes for accuracy on expert benchmarks with fully autonomous self-evolution. Omni optimizes for safe, auditable, human-supervised production deployment where irreversible actions (email, file writes) require verified human authorization and routing must be inspectable. Different operating point on the autonomy/auditability frontier.

---

### 5. Recommended Framing for Submission

**Title:** *"Toward Auditable and Human-Supervised Multi-Capability Agentic Systems: Design Principles and a Production Implementation"*

**Core argument:** Omni is an empirical validation of four co-dependent design principles that the existing literature has not studied together or measured:

| Principle | Measurable claim |
|---|---|
| **Routing Reliability** | Deterministic FSM routing reduces agent transition errors and routing latency vs. stochastic LLM routing |
| **Plan Grounding** | Continuous plan reconciliation against external execution artifacts prevents task drift on long-horizon tasks |
| **Schema-Enforced Communication** | Typed Pydantic contracts at inter-agent boundaries reduce failure cascades vs. free-form baseline |
| **Proportional Human Escalation** | Semantically differentiated HITL tiers reduce unnecessary interruptions while catching decision points requiring human judgment |

The Omni system is the existence proof that these four principles can co-exist in a single production-deployed system with five heterogeneous capabilities, without model fine-tuning, at inference time, on any backbone LLM.

**Target:** NeurIPS Systems track or ICLR (empirical ML) — not the theory track. The paper needs ablations before submission.

---

## Paper-by-Paper Comparison (Detailed)

### AutoGen (COLM 2024) — MUST CITE

**What they do:** Open-source framework for multi-agent LLM applications via customizable GroupChat and GroupChatManager. LLM-based role-play speaker selection. Validated on MATH, ALFWorld, OptiGuide.

| Dimension | AutoGen | Omni |
|---|---|---|
| Speaker selection | LLM call per turn (nondeterministic, costs tokens) | Hand-coded deterministic Python FSM (O(1), zero LLM calls) |
| HITL | Single `human_input_mode` parameter, sync stdin | 3-tier: plan confirmation + ambiguity resolution + terminal input, async WebSocket, 600s timeout |
| Memory across GroupChats | None — each GroupChat is isolated | OmniMemory: unified episodic store with `context_keeper` injection |
| GroupChat hierarchy | Flat — all agents in one GroupChat | 2-level: master GroupChat (11 agents) + per-capability sub-GroupChats |
| Inter-agent schema | Free-text strings | Pydantic models (PlannerResponse, AgentSelectionResponse, etc.) |
| Loop detection | None | LoopDetector sliding window in python_developer |

**RAVIAN's positioning:** Adopt AutoGen as substrate; frame RAVIAN as identifying and solving three production limitations of the base framework.

---

### MetaGPT (ICLR 2024 Oral) — MUST CITE

**What they do:** SOPs as structured prompt sequences for software-engineering agents. Typed artifact outputs (PRDs, UML, code). Publish-subscribe message pool. Executable feedback (3-retry unit test loop). SOTA on HumanEval/MBPP.

| Dimension | MetaGPT | Omni |
|---|---|---|
| Domain | Locked to software engineering pipeline | General-purpose: web, code, email, image, file |
| Execution topology | Linear assembly-line (static SOP) | Runtime LLM dependency analysis → sequential or parallel |
| Speaker selection | LLM pub-sub routing | Hand-coded deterministic FSM, zero LLM calls |
| HITL | None | 3-tier with 600s async timeout |
| Plan management | None across capabilities | plan_anchored loop: planner re-invoked after every capability |
| Memory propagation | Passive pub-sub (role-filtered retrieval) | Active episodic injection via OmniMemory |

**RAVIAN's positioning:** MetaGPT showed structured communication reduces hallucinations in single-domain pipelines. RAVIAN extends this to heterogeneous cross-domain orchestration plus HITL — properties outside MetaGPT's architectural scope.

---

### GPTSwarm (ICML 2024) — MUST CITE

**What they do:** Formalizes agents as optimizable DAGs. REINFORCE-based edge-level topology optimization. Node-level OPRO prompt refinement. Recursive swarm composition. Benchmarks: Mini Crosswords, HumanEval, GAIA.

| Dimension | GPTSwarm | Omni |
|---|---|---|
| Topology | Learned offline via REINFORCE | Fixed, deterministic FSM (hand-coded) |
| Routing cost | LLM inference per node transition | Zero — pure Python conditionals |
| Auditability | Learned policy, not inspectable | Every transition is a Python predicate, fully auditable |
| HITL | None | 3-tier with async WebSocket |
| Plan persistence | Implicit in graph topology | Explicit Pydantic PlannerResponse re-evaluated after each step |
| Production readiness | Research prototype | FastAPI + WebSocket + cancellation tokens + MongoDB |

**RAVIAN's positioning:** GPTSwarm optimizes topology offline for benchmark maximization. RAVIAN adopts a fixed FSM for production reliability and auditability, directing saved optimization budget toward HITL, plan-anchoring, and memory chaining.

---

### AFlow (ICLR 2025) — MUST CITE

**What they do:** MCTS over Python-encoded workflow graphs with LLM optimizer. 8 predefined Operators (Ensemble, Review, Revise, Test). 5.7% average improvement over hand-crafted baselines. Smaller models guided by AFlow match GPT-4o at 4.55% cost.

| Dimension | AFlow | Omni |
|---|---|---|
| Workflow discovery | Automated MCTS offline | Query-time LLM planning + deterministic FSM |
| Parallelism | Fixed in graph topology | LLM-driven runtime dependency detection per query |
| Mid-execution adaptation | None — graph is static | Continuous plan re-invocation after each capability |
| HITL | None | 3-tier with 600s async timeout |
| Memory | Search-tree traces (for optimizer, not runtime) | OmniMemory for runtime cross-capability context |

**RAVIAN's positioning:** AFlow solves *offline workflow discovery*. RAVIAN solves *online runtime adaptation* — incremental replanning, tiered human correction, and episodic memory propagation when executing against real-world services.

---

### Puppeteer / Evolving Orchestration (NeurIPS 2025) — MUST CITE

**What they do:** REINFORCE-trained centralized orchestrator dynamically sequences agents from a candidate pool. Reward = accuracy − λ·log(step_count). Induces emergent compaction (hub-dense) and cyclicality (iterative critique) in routing topology. SOTA on GSM-Hard, MMLU-Pro.

| Dimension | Puppeteer | Omni |
|---|---|---|
| Routing policy | Learned RL policy (stochastic) | Deterministic hand-coded FSM |
| Adaptability | Learns non-obvious orderings | Fixed transitions, requires human re-engineering |
| HITL | None | 3-tier with 600s timeout |
| Memory | Episode-level state only | OmniMemory: cross-capability episodic injection |
| Structured contracts | None (free-text) | Pydantic at every agent boundary |
| Target | Reasoning benchmarks | Production multi-capability personal automation |

**RAVIAN's positioning:** Puppeteer learns routing from outcomes; RAVIAN enforces routing through typed contracts and deterministic FSM, trading statistical optimality for predictability and auditability — properties demanded in enterprise deployment where actions have irreversible side effects.

---

### Reflexion (NeurIPS 2023) — MUST CITE

**What they do:** Verbal reinforcement learning. Agents generate self-critiques stored in episodic buffer. Actor conditions on reflections on next trial. 91% HumanEval pass@1. No gradient updates. Max 3–5 retrial cycles.

**Key distinction from RAVIAN:** Reflexion operates within single-task retry loops. Omni coordinates 11 specialized agents across 5 heterogeneous capabilities over 500-round sessions. RAVIAN's episodic memory propagates context *across capabilities within one task*, not *across trials of the same task*. No verbal self-critique in RAVIAN — all correction is grounded in external execution artifacts.

---

### Huang et al. "LLMs Cannot Self-Correct" (ICLR 2024) — MUST CITE

**What they do:** LLMs cannot improve reasoning via intrinsic self-correction. GPT-4 on GSM8K: 95.5% → 89.0% after self-correction. Distinguishes intrinsic (no external signal) from oracle-guided correction.

**How RAVIAN uses this finding as motivation:** This paper is a design axiom for Omni. RAVIAN's plan-anchored loop does not ask any agent to review its own free-text output. The planner receives actual capability execution results (browser HTML, Python stdout) as external signals and updates structured task statuses — structurally equivalent to the oracle condition the paper identifies as necessary for reliable correction.

**Recommended framing:** *"Huang et al. (2024) demonstrate that intrinsic self-correction degrades LLM accuracy. Omni takes this as a design axiom: every correction signal in our system is grounded in external execution artifacts, not model self-belief."*

---

### AgentOrchestra (arXiv 2025) — MUST CITE

**What they do:** Hierarchical multi-agent framework with TEA protocol. 5 specialized sub-agents. Self-evolution via textgrad. Dynamic tool synthesis (50+ tools at runtime). 89.04% on GAIA.

**Key advance over RAVIAN:** Self-evolution, dynamic tool synthesis, bidirectional role transformation (A2T, T2A, E2T, T2E, A2E, E2A).

**Key advance of RAVIAN:** 3-tier HITL (absent in AgentOrchestra), deterministic FSM routing, plan-anchored re-invocation, loop detection, production WebSocket infrastructure.

**Positioning:** "RAVIAN targets interactive, human-supervised agentic workflows; AgentOrchestra targets fully autonomous expert-level benchmarks. These are complementary operating points on the autonomy/auditability frontier."

---

### Magentic-UI (arXiv 2025) — MUST CITE (Closest peer)

**What they do:** AutoGen Magentic-One with 6 HITL interaction modes including action guards and co-planning. WebSocket streaming. ChromaDB cross-session memory.

**This is the closest architectural peer to Omni.** Differentiation:
- RAVIAN: deterministic FSM (zero LLM routing calls) vs. Magentic-UI: LLM-based Orchestrator
- RAVIAN: Pydantic schema enforcement at all boundaries vs. Magentic-UI: free-form messages
- RAVIAN: plan-anchored continuous replanning vs. Magentic-UI: user-initiated plan edits

---

### MemGPT (arXiv 2023) — MUST CITE

**What they do:** OS-inspired memory hierarchy (main context, recall, archival) for single LLM agents. Self-directed memory tool calls to manage context window.

**Key distinction:** MemGPT solves intra-agent long-context continuity. Omni solves inter-agent episodic handoff across isolated GroupChat instances — a problem that does not exist in single-agent settings.

---

### Magentic-UI, ReConcile, HULA, SWE-agent, CowPilot, ARIA, TravelPlanner, HiAgent

| Paper | Relation to RAVIAN | Citation |
|---|---|---|
| **ReConcile** (ACL 2024) | Consensus for homogeneous reasoning agents. RAVIAN: sequential pipeline of heterogeneous agents for real-world tool use. Orthogonal. | should-cite |
| **HULA** (arXiv 2024) | First industrial HITL for coding agents (2 fixed stages). RAVIAN generalizes to 3 tiers + dynamic triggering. | should-cite |
| **SWE-agent** (NeurIPS 2024) | ACI design for coding. Loop-breaking via linting feedback. RAVIAN's LoopDetector is analogous but capability-agnostic. | should-cite |
| **CowPilot** (arXiv 2025) | 15.2% human effort → 95% accuracy. Validates tiered HITL efficiency claim. RAVIAN's 3 tiers address CowPilot's single-tier limitation. | should-cite |
| **ARIA** (arXiv 2025) | Uncertainty-driven HITL elicitation for knowledge adaptation. Complementary axis: knowledge vs. orchestration. | should-cite |
| **TravelPlanner** (ICML 2024) | 0.6% GPT-4 success on constrained planning. Motivates RAVIAN's plan-anchored architecture. | should-cite |
| **HiAgent** (arXiv 2024) | Hierarchical working memory for long-horizon tasks. Single-env, single-agent. RAVIAN: multi-agent, heterogeneous caps, HITL. | should-cite |
| **ST-WebAgentBench** (arXiv 2024) | 66% policy-compliant completion. Motivates HITL as safety pre-condition. | should-cite |
| **OSWorld** (NeurIPS 2024) | Multi-application long-horizon benchmark. Motivates RAVIAN's programmatic orchestration design. | should-cite |

---

## Full References (35 Papers, Sorted Newest First)

### 2025

1. **Magentic-UI: Towards Human-in-the-Loop Agentic Systems** — Microsoft Research, 2025. AutoGen-based multi-agent UI implementing co-planning, co-tasking, and action-guard HITL tiers.

2. **Multi-Agent Collaboration via Evolving Orchestration (Puppeteer)** — NeurIPS 2025. RL-trained orchestration policy for dynamic agent selection in multi-agent collaboration pipelines.

3. **Multi-Agent Collaboration Mechanisms: A Survey of LLMs** — arXiv Jan 2025. Five-dimensional taxonomy of LLM multi-agent systems across actors, collaboration type, structure, coordination, and communication.

4. **AgentRefine: Enhancing Agent Generalization through Refinement Tuning** — arXiv 2025. Fine-tuning framework teaching agents to refine trajectories from environment feedback signals.

5. **ReflectEvo: Improving Meta Introspection of Small LLMs by Learning Self-Reflection** — arXiv 2025. SFT+DPO approach to training small LLMs to perform multi-turn self-reflection.

6. **AgentOrchestra: Orchestrating Multi-Agent Intelligence with the TEA Protocol** — arXiv 2025. Hierarchical planner-over-sub-agents with TEA protocol, self-evolution, and dynamic tool synthesis. 89.04% GAIA.

7. **CowPilot: A Framework for Autonomous and Human-Agent Collaborative Web Navigation** — arXiv 2025. Mixed-initiative web navigation with human override via pause-and-redirect gestures. 15.2% human effort → 95% accuracy.

8. **ARIA: Enabling Self-Improving Agents via Human-In-The-Loop Guidance** — arXiv 2025. Confidence-gated expert solicitation for test-time knowledge adaptation.

9. **Plan-and-Act: Improving Planning of Agents for Long-Horizon Tasks** — arXiv 2025. Two-model architecture separating Planner LLM from Executor LLM for long-horizon task completion.

10. **DeepSeek-R1: Incentivizing Reasoning Capability via Reinforcement Learning** — arXiv 2025. Large-scale RL training producing emergent chain-of-thought self-reflection.

11. **LLM-Based Human-Agent Collaboration and Interaction Systems: A Survey** — arXiv 2025. Survey defining delegator, supervisor, and collaborator human roles in LLM-based human-agent systems.

12. **Planning with Schemas (PwS): The Cognitive Bandwidth Bottleneck** — arXiv 2025. Schema-based planning replacing action enumeration with structured intermediate representations.

### 2024

13. **AFlow: Automating Agentic Workflow Generation** — ICLR 2025. MCTS-driven search over Python-encoded workflow graphs. 5.7% average improvement over hand-crafted baselines.

14. **AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation** — COLM 2024. Foundational open-source framework for multi-agent LLM applications via customizable GroupChat.

15. **MetaGPT: Meta Programming for A Multi-Agent Collaborative Framework** — ICLR 2024 (Oral). Role-playing multi-agent software engineering system with typed intermediate artifacts.

16. **GPTSwarm: Language Agents as Optimizable Graphs** — ICML 2024 (Oral). Graph-based multi-agent system treating agent pipelines as optimizable computation graphs.

17. **HULA: Human-In-the-Loop Software Development Agents** — arXiv 2024. Two-stage AI planning + coding agent pipeline with human approval gates at plan and code review.

18. **Enhancing LLM Planning Capabilities through Intrinsic Self-Critique** — arXiv 2024. Prompting-based self-critique loop for LLM plan generation using domain preconditions.

19. **Meta-Rewarding Language Models: Self-Improving Alignment with LLM-as-Meta-Judge** — arXiv 2024. Iterative DPO training where the model judges both response quality and judgment quality.

20. **Large Language Models Cannot Self-Correct Reasoning Yet** — ICLR 2024. Empirical demonstration that intrinsic LLM self-correction degrades performance absent external feedback.

21. **Self-Reflection in LLM Agents: Effects on Problem-Solving Performance** — arXiv 2024. Controlled study isolating eight self-reflection types as independent variables affecting problem-solving.

22. **Self-Evolved Reward Learning for LLMs (SER)** — arXiv 2024. Iterative reward model retraining on self-generated preference data.

23. **Training Language Models to Self-Correct via Reinforcement Learning (SCoRe)** — arXiv 2024. Two-attempt RL training for genuine self-correction on math and coding benchmarks.

24. **TravelPlanner: A Benchmark for Real-World Planning with Language Agents** — ICML 2024. Multi-constraint travel planning benchmark. GPT-4 achieves 0.6% end-to-end success.

25. **ST-WebAgentBench: A Benchmark for Safety and Trustworthiness in Web Agents** — arXiv 2024. Enterprise-grade web agent benchmark introducing Completeness under Policy (CuP) metric.

26. **OSWorld: Benchmarking Multimodal Agents for Open-Ended Tasks in Real Computer Environments** — NeurIPS 2024. Large-scale benchmark for agents executing heterogeneous real desktop OS tasks.

27. **SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering** — NeurIPS 2024. Interface design study showing deliberate ACI design dramatically improves coding performance.

28. **HiAgent: Hierarchical Working Memory Management for Long-Horizon Agent Tasks** — arXiv 2024. Subgoal-guided working memory compression. 2x success-rate, 35% fewer tokens.

29. **ReConcile: Round-Table Conference Improves Reasoning via Consensus among Diverse LLMs** — ACL 2024. Multi-agent round-table discussion eliciting consensus from diverse LLMs for complex reasoning.

30. **MLDT: Multi-Level Decomposition for Complex Long-Horizon Robotic Task Planning** — arXiv 2024. Three-level goal/task/action decomposition for robotic planning with open-source LLMs.

31. **LaMMA-P: Generalizable Multi-Agent Long-Horizon Task Allocation with LM-Driven PDDL** — arXiv 2024. LLM-driven PDDL planner for multi-robot long-horizon task allocation.

32. **ReAcTree: Hierarchical LLM Agent Trees with Control Flow for Long-Horizon Planning** — arXiv 2024. Hierarchical agent trees with sequential and parallel control-flow nodes. ~2x flat ReAct success.

33. **Human-Inspired Episodic Memory for Infinite Context LLMs (EM-LLM)** — arXiv 2024. Bayesian surprise-based event segmentation for LLM episodic memory with infinite context.

### 2023

34. **Reflexion: Language Agents with Verbal Reinforcement Learning** — NeurIPS 2023. Verbal RL framework where agents store self-critiques in reflection buffer without weight updates.

35. **MemGPT: Towards LLMs as Operating Systems** — arXiv 2023. OS-inspired memory hierarchy for LLMs enabling context window management via paged in-memory storage.

---

*Generated from a 66-agent research sweep: 63 papers found · 35 compared · 463 tool uses · ~16 minutes · June 2026*
