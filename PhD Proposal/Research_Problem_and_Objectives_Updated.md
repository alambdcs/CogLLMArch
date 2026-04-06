# Updated Research Problem and Objectives
## Aligned with CRL + VLA Integration + Uncertainty-Budgeted Causal Reliability Objective (UBCRO)

Date: April 6, 2026

---

## 1) Updated Research Problem Statement

Long-horizon embodied decision making remains unreliable because current systems treat planning, prediction, memory, and low-level control as partially disconnected processes. Language planners produce coherent but occasionally non-executable plans; predictive models degrade over rollout depth; memory retrieval is often similarity-based rather than causally grounded; and VLA policies are frequently used as downstream executors without full participation in high-level decision arbitration.

### Core problem
The central unresolved problem is:

**How can an embodied agent jointly generate, evaluate, and execute long-horizon action sequences under uncertainty, while continuously refining behavior through causally grounded memory and risk-aware arbitration?**

This proposal addresses the problem by introducing a unified decision framework where:
1. high-level planning is perception-grounded and structurally constrained,
2. world-model prediction is uncertainty-calibrated,
3. memory contributes causal (not only semantic) guidance,
4. VLA execution is integrated bidirectionally,
5. and all modules are optimized under a shared constrained objective (UBCRO).

---

## 2) Updated Research Questions (RQ1–RQ5)

### RQ1 (Grounded Planning)
To what extent does perception-grounded, constraint-aware language planning improve structural validity and physical executability compared with unconstrained language planning?

### RQ2 (Predictive Reliability)
How accurately can uncertainty-calibrated world models estimate plan feasibility over long horizons, and how does prediction bias influence final task success?

### RQ3 (Causal Memory)
Can causal episodic memory with structured contribution tracing reduce repeated failures and improve adaptation compared with similarity-based reflection?

### RQ4 (Integration Effects)
What is the contribution of each component (planning, prediction, memory, VLA, arbitration), and do integrated interactions yield statistically significant reliability gains beyond additive effects?

### RQ5 (Uncertainty Propagation and Safety)
How does uncertainty from perception, prediction, memory, and VLA execution propagate through the decision loop, and can uncertainty-budgeted arbitration improve reliability-safety trade-offs?

---

## 3) Updated Objectives

## Objective 1: Build a perception-grounded structured planner
- Develop a typed Plan Intermediate Representation (PIR) with explicit preconditions/effects.
- Enforce affordance and state constraints during plan generation.
- Support failure-type-aware local repair (not only full replanning).

**Outcome target:** increase executable plan ratio and reduce invalid action chains.

## Objective 2: Develop uncertainty-calibrated predictive feasibility evaluation
- Train ensemble latent world models for long-horizon rollout scoring.
- Separate epistemic and aleatoric uncertainty in feasibility estimation.
- Integrate calibration-aware metrics into branch pruning and ranking.

**Outcome target:** improve plan ranking correlation with realized return and reduce catastrophic mispredictions.

## Objective 3: Implement causal memory and reflection
- Build Causal Episodic Memory (CEM) with contribution vectors and confidence decay.
- Perform counterfactual reflection for failure attribution.
- Prioritize retrieval by expected causal utility.

**Outcome target:** reduce repeated errors and improve sample efficiency across repeated tasks.

## Objective 4: Integrate VLA as a first-class decision module
- Add a VLA Policy Adapter with bidirectional interface (action feasibility, confidence, OOD flags, intervention requests).
- Couple high-level PIR steps to short-horizon visuomotor skill execution.
- Enable arbitration-triggered fallback behaviors when VLA uncertainty is high.

**Outcome target:** improve execution robustness and reduce sim/embodiment mismatch failures.

## Objective 5: Optimize unified uncertainty-budgeted arbitration
- Implement UBCRO-based receding-horizon selection with hard risk constraints.
- Fuse module uncertainties through an Uncertainty Bus.
- Enforce chance-constrained selection and selective information-gathering steps under high uncertainty.

**Outcome target:** better reliability-safety trade-off under partial observability and noise.

---

## 4) Mathematical Objective Mapping (Problem → Objectives)

Let \(\pi\) denote candidate action sequence and \(\Pi\) plan set. The overall optimization is:
\[
\pi^{\star}=\arg\max_{\pi\in\Pi}
\mathcal{A}(\pi)+\mathcal{F}(\pi)+\mathcal{C}(\pi)-\lambda_u\mathcal{U}(\pi)-\lambda_x\mathcal{X}(\pi)
\]
subject to:
\[
\sum_{t=1}^{H} b_t(\pi) \le B,
\qquad
\Pr(\text{failure}\mid\pi)\le\delta.
\]

Objective mapping:
- **Obj1 → \(\mathcal{A}(\pi)\)** (goal-plan grounding and executability consistency)
- **Obj2 → \(\mathcal{F}(\pi),\mathcal{U}(\pi)\)** (predictive return + calibrated uncertainty)
- **Obj3 → \(\mathcal{C}(\pi)\)** (causal memory gain)
- **Obj4 → \(\mathcal{X}(\pi)\)** reduction via VLA-aware interface consistency
- **Obj5 → constraints \((B,\delta)\)** and uncertainty-aware policy control

---

## 5) Hypotheses Corresponding to Objectives

- **H1:** Constraint-aware grounded planning yields higher executability than unconstrained LLM planning.
- **H2:** Ensemble uncertainty-calibrated prediction improves long-horizon plan selection over single-model scoring.
- **H3:** Causal memory reflection reduces repeated failure recurrence compared with similarity-only retrieval.
- **H4:** VLA-in-the-loop arbitration outperforms “planner-then-executor” pipelines in long-horizon reliability.
- **H5:** Uncertainty-budgeted arbitration reduces catastrophic failures while preserving competitive task completion.

---

## 6) Scope and Contribution Boundary

This updated Research Problem and Objectives section defines novelty at the **integration and constrained decision formulation** level:
- not claiming each module is individually new,
- claiming that their joint uncertainty-budgeted, causal, and VLA-integrated optimization is the novel research contribution.

This framing is designed to be both ambitious and defensible for PhD scholarship evaluation.
