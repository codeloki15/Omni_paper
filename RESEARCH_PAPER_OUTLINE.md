# Research Paper Outline: Omni — A Self-Aligning Multi-Agent System for Long-Horizon Tasks

> **Target venues:** ICLR 2026 · NeurIPS 2026 · ICML 2026  
> **Track suggestions:** Agents & Tool Use · Human-AI Interaction · Multi-Agent Systems · LLM Reliability

---

## 1. The Core Claim (One Paragraph Pitch)

We present **Omni**, a production multi-agent system that coordinates 11 specialized LLM agents to complete complex, long-horizon tasks without requiring per-step human supervision. The system introduces three novel mechanisms that together enable reliable task completion: (1) a **plan-anchored self-alignment loop** that continuously reconciles agent actions with an evolving structured plan, (2) **structured HITL gates** that surface the system to a human only when genuine ambiguity or risk is detected, and (3) a **deterministic finite-state speaker-selection function** that replaces stochastic agent coordination with explicit, verifiable routing logic. Together these mechanisms enable the system to autonomously complete multi-capability tasks — spanning web browsing, code execution, email, image generation, and file search — over sessions that may span hundreds of conversational turns.

---

## 2. Problem Statement

### 2.1 What the Paper Is Solving

Current LLM agent systems fail at long-horizon tasks for three reasons:

| Failure mode | How it manifests |
|---|---|
| **Drift** | Agent loses track of the original goal after several tool calls |
| **Over-reliance on humans** | Asks for confirmation at every step, defeating autonomy |
| **Under-reliance on humans** | Proceeds on ambiguous input and produces wrong results irreversibly |
| **Coordination chaos** | In multi-agent settings, agents produce redundant or conflicting work |
| **Context loss** | State from early steps is unavailable to later agents |

### 2.2 The Research Gap

Existing frameworks (AutoGen, LangGraph, CrewAI) provide orchestration primitives but leave the **self-correction loop, HITL placement strategy, and speaker-selection policy** entirely to the developer. Omni embeds these as first-class, principled components.

---

## 3. Contributions Worth Highlighting to Reviewers

### Contribution 1 — Plan-Anchored Self-Alignment Loop

**What it is:**  
The `planner_agent` creates a structured `PlannerResponse` (a Pydantic model with per-task status fields) before execution begins. After every capability returns, the planner is re-invoked with the result and required to update statuses in-place. The `agent_aligner` reads the updated plan to decide whether to continue, request human input, or conclude.

**Why it matters:**  
This creates a closed feedback loop where the task plan acts as a shared ground truth. Individual agents cannot drift from the objective because the plan is the only signal `agent_aligner` trusts to route forward.

**Relevant files:**  
`src/omni/manager.py:757–831` (speaker selection), `src/omni/prompts.py:211` (plan update instruction), `src/omni/response_format.py` (PlannerResponse schema)

**Framing for reviewers:**  
Analogous to scratchpad reasoning (Nye et al., 2021) but applied at the orchestration level — the plan is a persistent, structured scratchpad visible to all agents.

---

### Contribution 2 — Tiered Human-in-the-Loop (HITL) Architecture

**What it is:**  
HITL is not a uniform interrupt — the system distinguishes three tiers:

| Tier | Agent | Trigger | Human cost |
|---|---|---|---|
| **Plan confirmation** | `get_user_acceptance` | Once per task, after planning | Low (approve/reject) |
| **Ambiguity resolution** | `manual_verification` | When any agent signals uncertainty or risk | Medium (specific question) |
| **Terminal input** | `user_input` | After manual_verification | Low (typed response) |

**Why it matters:**  
Most HITL research treats human involvement as binary (fully autonomous vs. fully supervised). This system operationalizes a *proportional* model: humans are invoked for *decision-point inputs* not *progress tracking*. The 600-second timeout on user input (`connection.py`) quantifies the system's willingness to wait vs. time-out and retry.

**Relevant files:**  
`src/managers/connection.py:_create_input_func()`, `src/omni/prompts.py:301–394` (agent_aligner HITL decision logic), `src/omni/manager.py:801–810`

**Framing for reviewers:**  
Extends work on mixed-initiative systems (Horvitz, 1999) and complements recent HITL-agent papers (Yao et al., 2022 ReAct) by specifying exactly *when* and *how* human feedback is solicited.

---

### Contribution 3 — Deterministic Finite-State Speaker Selection

**What it is:**  
Rather than asking an LLM "who should speak next?" (as in vanilla AutoGen GroupChat), Omni implements `custom_speaker_selection_func` — a hand-coded FSM in Python that routes between agents based on message content predicates.

```
judge_question ──(task)──► planner_agent ──► get_user_acceptance ──► agent_aligner
                                                                           │
              ┌────────────────────────────────────────────────────────────┤
              ▼                                                             │
        agent_router ──► agent_caller ──► agent_executor ──► planner_agent ─┘
              │
              ▼
        manual_verification ──► user_input ──► agent_aligner
              
        planner(status=completed) ──► process_completion_agent
```

**Why it matters:**  
FSM routing is: (a) verifiable — the routing graph can be formally audited, (b) debuggable — failures are traceable to specific transitions, (c) token-efficient — no LLM call needed for routing. This contrasts with learned/emergent coordination (Minsky, 1986; Park et al., 2023 Generative Agents) and is closer to classical workflow engines, but built on top of LLM agents.

**Relevant files:**  
`src/omni/manager.py:756–831`, `src/omni/prompts.py:346–393`

**Framing for reviewers:**  
Provides an empirically testable alternative hypothesis to *emergent coordination*: deterministic routing may be more reliable for production use cases even if less "intelligent."

---

### Contribution 4 — Memory-Mediated Capability Chaining

**What it is:**  
`OmniMemory` is a session-scoped episodic store that indexes all messages by `(chat_id, capability_name, sequence_id)`. When a capability is invoked, `capability_manager.py` calls `memory.pull_recent_from_executor()` and injects prior execution context as a synthetic `context_keeper` message into the new capability's chat.

```python
# capability_manager.py
omni_executor = self.memory.pull_recent_from_executor()
for agent_message in omni_executor:
    messages.append({"role": "user", "name": "context_keeper", "content": agent_message["content"]})
```

**Why it matters:**  
This allows downstream capabilities to reason over results from upstream capabilities without explicit data passing — the memory layer acts as an implicit context bus. A Python coder capability automatically knows what a prior browser session retrieved.

**Relevant files:**  
`src/omni/memory.py` (781 lines), `src/omni/capability_manager.py`

**Framing for reviewers:**  
Related to episodic memory in cognitive architectures (Tulving, 1972; OpenCog); in LLM terms closest to MemGPT (Packer et al., 2023) but applied across heterogeneous capability agents rather than within a single agent.

---

### Contribution 5 — Dependency-Aware Parallel Execution

**What it is:**  
The `agent_router` prompt instructs the LLM to analyze the planner's task breakdown for inter-task dependencies. If tasks are independent → return all task IDs (parallel). If task B depends on task A → return only task A's ID (sequential). This is the only LLM-driven routing in an otherwise deterministic system.

**Why it matters:**  
Dynamic dependency analysis at runtime allows the system to adapt execution topology to the task, not to a fixed workflow graph. This bridges the gap between rigid DAG-based workflow engines and fully dynamic agent systems.

**Relevant files:**  
`src/omni/prompts.py:398–511` (agent_router prompt)

---

### Contribution 6 — Loop Detection in Code Execution

**What it is:**  
`LoopDetector` in `src/capabilities/python_developer/manager.py` maintains a sliding window of recent messages and detects repeated content patterns. When a loop is detected, the Python developer capability exits gracefully rather than exhausting the context window.

**Why it matters:**  
Code execution loops are a common failure mode in code-generating agents. This is a lightweight, empirically motivated safety mechanism — relevant for benchmarks like SWE-bench where agents often get stuck.

**Relevant files:**  
`src/capabilities/python_developer/manager.py`

---

### Contribution 7 — Structured Output Contracts Between Agents

**What it is:**  
Every agent in Omni responds with a Pydantic model enforced via `response_format`:
- `planner_agent` → `PlannerResponse` (task breakdown, statuses, time estimates)
- `agent_aligner` → `AgentSelectionResponse` (chain-of-thought steps + final routing decision)
- `judge_question` → `QuestionClassification` (task_oriented vs. conversation)

**Why it matters:**  
Structured contracts between agents prevent the cascading parsing failures common in free-text multi-agent systems. An agent that receives malformed input from another agent is a silent failure mode — Pydantic validation makes it explicit and catchable.

**Relevant files:**  
`src/omni/response_format.py`

---

## 4. Experimental Claims to Develop

These are empirically testable claims that would make the paper concrete for reviewers:

### Claim A — Self-Alignment Reduces Task Abandonment Rate
**Setup:** Run the system on a task suite (e.g., 50 multi-step tasks) with and without the plan-update loop. Measure task completion rate and number of rounds to completion.

**Hypothesis:** Plan-anchored systems complete more tasks and require fewer rounds.

### Claim B — Tiered HITL Reduces Unnecessary Human Interrupts vs. Full Autonomy Errors
**Setup:** Compare 3 conditions: (1) full autonomy, (2) OMNI tiered HITL, (3) interrupt-at-every-step. Measure task success rate and human interrupt count.

**Hypothesis:** Tiered HITL achieves near-full-autonomy success rate with dramatically fewer interrupts than condition 3.

### Claim C — FSM Routing Outperforms LLM-Based Routing on Reliability
**Setup:** Replace `custom_speaker_selection_func` with an LLM-based selector (as in default AutoGen). Run both on 50 tasks. Measure routing errors (wrong agent called), token cost, and task success.

**Hypothesis:** FSM routing has fewer routing failures and lower token cost.

### Claim D — Memory-Mediated Context Chaining Improves Multi-Capability Task Success
**Setup:** Disable `context_keeper` injection. Run tasks requiring capability chaining (e.g., "browse this site, then write Python to analyze the data"). Measure success rate.

**Hypothesis:** Without memory chaining, cross-capability tasks fail at higher rates.

---

## 5. Architectural Diagram (for Paper Figure 1)

```
User Message
     │
     ▼
[judge_question] ──── conversation? ────► [conversation_agent]
     │
     task_oriented
     ▼
[planner_agent] ──────────────────────────────────┐
     │                                             │ (result feedback loop)
     ▼                                             │
[get_user_acceptance] ◄── human confirms plan      │
     │                                             │
     ▼                                             │
[agent_aligner] ◄──────────────────────────────────┘
     │
     ├──── pending tasks? ──────► [agent_router]
     │                                 │
     │                          dependency analysis
     │                                 │
     │                     ┌───────────┴──────────┐
     │                  sequential              parallel
     │                     │                      │
     │                     ▼                      ▼
     │              [agent_caller]         [agent_caller ×N]
     │                     │
     │               tool invocation
     │              ┌──────┴───────┐
     │              │              │
     │        [agent_executor]  (direct result)
     │              │
     │         capability sub-chat
     │         ┌────────────┐
     │         │ OmniMemory │◄── all messages stored
     │         └────────────┘
     │              │
     │         planner_agent (plan update)
     │
     ├──── human needed? ──────► [manual_verification] ──► [user_input] ──► back to aligner
     │
     └──── all done? ──────────► [process_completion_agent]
```

---

## 6. Related Work to Position Against

| Paper | How Omni differs |
|---|---|
| **ReAct** (Yao et al., 2022) | ReAct is single-agent; Omni is 11-agent with explicit coordination |
| **AutoGen** (Wu et al., 2023) | AutoGen provides GroupChat but leaves routing policy undefined; Omni specifies a verifiable FSM |
| **MemGPT** (Packer et al., 2023) | MemGPT manages memory within one agent; Omni shares episodic memory *across* capabilities |
| **Generative Agents** (Park et al., 2023) | Emergent coordination in simulation; Omni uses deterministic routing for production reliability |
| **HITL Survey** (Monarch, 2021) | Describes HITL patterns abstractly; Omni implements a concrete tiered model |
| **SWE-bench** (Jimenez et al., 2023) | Benchmark for code agents; loop detection in python_developer is directly relevant |
| **LangGraph** (Chase, 2023) | Graph-based agent orchestration; Omni uses code-defined FSM + LLM-driven dependency analysis |
| **TaskBench** (Shen et al., 2023) | Task decomposition benchmark; planner_agent's structured breakdowns are a direct contribution here |

---

## 7. Potential Paper Titles

1. **"Omni: Plan-Anchored Self-Alignment in Multi-Agent LLM Systems"**
2. **"Deterministic Routing Meets Learned Reasoning: A Hybrid Architecture for Reliable Multi-Agent Task Completion"**
3. **"OMNI: Tiered Human-in-the-Loop Control for Long-Horizon Agentic Tasks"**
4. **"From Plans to Actions: Self-Aligning Multi-Agent Systems via Structured Feedback Loops"**
5. **"When to Ask: Proportional HITL in Production Multi-Agent Systems"**

---

## 8. Venue-Specific Framing

### ICLR (learning-focused)
Lead with **Contribution 3 (FSM routing vs. learned routing)** as an empirical finding. Frame the paper as: "We show that for task-completion agents, deterministic routing with learned sub-components outperforms fully-learned coordination." Emphasize the ablation studies in Section 4.

### NeurIPS (systems + methods)
Lead with **Contributions 1 + 2 (plan-anchored alignment + tiered HITL)**. Frame as: "We present a system-level design for multi-agent LLM orchestration and evaluate its components." Emphasize the architecture, the practical lessons, and the deployment insights. NeurIPS datasets/benchmarks track would welcome a task-completion benchmark.

### ICML
Lead with **Contribution 5 (dependency-aware execution)**. Frame as: "We show that runtime dependency analysis — using an LLM as a dependency solver — enables adaptive execution topology in multi-agent systems." Position alongside planning and scheduling literature.

---

## 9. Key Code Evidence (for Methods Section)

| Claim | File | Lines | Evidence |
|---|---|---|---|
| Plan update loop | `src/omni/prompts.py` | 211 | `"UPDATE THE PLAN BASED ON THE OUTCOMES"` |
| FSM routing | `src/omni/manager.py` | 757–831 | `custom_speaker_selection_func` |
| HITL tiering | `src/omni/prompts.py` | 301–394 | `agent_aligner` decision logic XML |
| HITL timeout | `src/managers/connection.py` | `_create_input_func` | `asyncio.wait_for(..., timeout=600)` |
| Memory chaining | `src/omni/capability_manager.py` | `_prepare_chat_messages` | `context_keeper` injection |
| Loop detection | `src/capabilities/python_developer/manager.py` | `LoopDetector` class | Sliding window check |
| Structured contracts | `src/omni/response_format.py` | Full file | Pydantic models for all agents |
| Max rounds (500) | `src/configs/system_ai_config.py` | `get_max_round()` | Long-horizon support |
| Cancellation | `src/managers/cancellation_token.py` | Full file | Graceful shutdown with callbacks |
| Token tracking | `src/omni/manager.py` | `_calculate_agent_usage_delta` | Per-agent usage deltas |

---

## 10. Limitations to Acknowledge (Strengthens the Paper)

1. **No formal verification of the FSM** — the routing graph is hand-coded and could have unreachable states or missing transitions.
2. **HITL quality not measured** — the paper does not study whether human verifiers improve output quality or just user trust.
3. **Single benchmark** — current evaluation is qualitative; a quantitative benchmark suite would strengthen claims.
4. **LLM dependency in planner** — the plan-update loop quality depends on the underlying LLM's ability to maintain structured JSON across many turns.
5. **Scalability** — the architecture is tested with 5 capabilities; behavior with 50+ capabilities is unknown.

---

## 11. Suggested Abstract (Draft)

> We present Omni, a multi-agent system for completing long-horizon tasks that require coordinating heterogeneous capabilities including web browsing, code execution, email management, and image generation. Unlike prior multi-agent frameworks that rely on emergent or learned coordination, Omni introduces three principled mechanisms: (1) a plan-anchored self-alignment loop in which a planner agent maintains a structured, continuously-updated task graph as a shared ground truth for all agents; (2) a tiered human-in-the-loop (HITL) architecture that surfaces humans only at decision-critical junctures — plan confirmation, ambiguity resolution, and risk checkpoints — rather than at fixed intervals; and (3) a deterministic finite-state speaker-selection function that replaces stochastic LLM-based routing with a verifiable transition graph. We further contribute a memory-mediated context propagation mechanism that enables downstream capabilities to reason over upstream results without explicit data passing. Empirically, we show that [CLAIMS TO BE FILLED FROM EXPERIMENTS]. Our system supports sessions up to 500 conversational rounds with graceful cancellation, structured output contracts between agents, and per-agent token tracking. We release the full implementation and benchmark suite.

---

*Document generated from direct code analysis of `feature/browser_2.0` branch, June 2026.*
