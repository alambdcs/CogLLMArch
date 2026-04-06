# Updated Research Design and Methodology
## Toward Cognitive Architectures for Reliable Long-Horizon Planning in LLM-Based Embodied Agents

This updated section deepens the methodology around the five research questions (RQ1–RQ5) and corresponding objectives by specifying: (i) precise gaps in existing directions, (ii) realistic technical interventions, (iii) measurable evaluation protocols, and (iv) an integrated architecture with explicit interfaces and learning loops.

---

## 1) Deep Gap Analysis by Research Direction

### 1.1 Language-Based Planning (RQ1 focus)

#### Identified gaps
1. **Linguistic plausibility vs. physical executability mismatch**  
   LLM planners produce syntactically coherent plans that violate object affordances, preconditions, geometric constraints, and temporal ordering.
2. **Weak grounding of symbols to scene state**  
   Plans often reference entities not perceptually confirmed (hallucinated objects), or ignore latent environment states (e.g., container closed/open).
3. **No explicit plan validity structure**  
   Most approaches produce a flat action list without formal representations for preconditions, effects, and resource constraints.
4. **Poor recovery from partial failure**  
   Replanning is often ad hoc, lacking structured diagnosis of why a step failed.
5. **Distribution shift sensitivity**  
   Prompt-only methods degrade in novel task compositions and long-horizon dependency chains.

#### Realistic solutions
1. **Typed Skill Graph + Constraint Decoder**  
   Constrain decoding with a typed action grammar: `(action, object, target, tool, state-precondition)` and hard filters from affordance models. This transforms planning from unconstrained text generation to structured decoding.
2. **Dual grounding loop (symbolic + perceptual)**  
   Each candidate step must pass a grounding check against scene graph beliefs (objects, relations, confidence). Unverified entities trigger either an observation action or an alternate plan branch.
3. **Plan Intermediate Representation (PIR)**  
   Maintain plan as a graph with causal links and temporal edges rather than plain text sequence. The PIR supports executability checks, dependency-aware repair, and traceable metrics.
4. **Failure taxonomy + targeted replanning**  
   Classify failures into perception failure, execution failure, and model mismatch; use failure-specific repair operators (re-detect, re-grasp, reorder, substitute tool/action).
5. **Curriculum for compositional planning**  
   Train/finetune with increasing horizon length and constraint density; include adversarial perturbations (missing object, blocked path, delayed effect) to improve robustness.

#### Expected outcomes / hypotheses
- Structural correctness and executability improve significantly versus unconstrained LLM planning.
- Replanning cost decreases because failure localization becomes explicit in PIR.

---

### 1.2 Predictive / World-Model Evaluation (RQ2 and RQ5 focus)

#### Identified gaps
1. **Compounding rollout error**  
   Multi-step prediction drifts from true dynamics, making long-horizon feasibility scoring unreliable.
2. **Confounded uncertainty**  
   Existing methods often do not separate aleatoric (environment stochasticity) and epistemic (model ignorance) uncertainty.
3. **Insufficient coupling to planner search**  
   Prediction models evaluate plans post hoc rather than guiding branching and pruning during planning.
4. **Sparse calibration reporting**  
   Accuracy is reported, but probability calibration (critical for risk-sensitive decisions) is often neglected.
5. **Poor counterfactual reliability**  
   Models may rank feasible-looking but causally impossible action chains too highly.

#### Realistic solutions
1. **Latent ensemble world model**  
   Use an ensemble of latent dynamics models with shared encoder and diversified heads to estimate epistemic uncertainty from disagreement.
2. **Uncertainty-aware feasibility scoring**  
   Score candidate plans by expected utility minus uncertainty penalty: 
   \[
   S_{pred}(\pi)=\mathbb{E}[R(\pi)]-\lambda_{u}U_{epi}(\pi)-\lambda_{a}U_{ale}(\pi)
   \]
3. **Branch-and-bound planning with predictive priors**  
   Integrate model rollout at expansion time: prune branches crossing uncertainty or inconsistency thresholds.
4. **Calibration-aware training objective**  
   Add proper scoring rules (NLL/Brier/ECE regularization) and evaluate reliability diagrams to ensure uncertainty is decision-useful.
5. **Consistency critics**  
   Train a trajectory consistency critic to penalize state transitions violating known physics/task invariants.

#### Expected outcomes / hypotheses
- Improved plan ranking quality at medium and long horizons.
- Lower catastrophic failure rates under partial observability and perturbations due to calibrated risk penalties.

---

### 1.3 Memory and Reflection (RQ3 focus)

#### Identified gaps
1. **Similarity-based retrieval without causal relevance**  
   Memories are often selected by semantic closeness, not by contribution to success/failure.
2. **No structured credit assignment**  
   Systems rarely assign responsibility across multi-step episodes.
3. **Memory contamination and contradiction**  
   Inconsistent episodes coexist without confidence reconciliation.
4. **Unbounded memory growth**  
   Retrieval degrades over time due to noisy accumulation.
5. **Weak transfer across domains**  
   Reflections become overly context-specific and brittle in new environments.

#### Realistic solutions
1. **Causal Episodic Memory (CEM) schema**  
   Store episodes as tuples: `(context, plan fragment, predicted outcome, actual outcome, delta, attribution vector, confidence)`.
2. **Temporal difference contribution tracing**  
   Use stepwise advantage decomposition to estimate each action’s contribution to terminal success.
3. **Counterfactual reflection engine**  
   Generate minimal-action counterfactuals (“what if step k changed?”) using world model rollouts; store only high-confidence causal deltas.
4. **Memory governance**  
   Introduce write filters, contradiction resolution, and compression by abstracting recurring patterns into reusable strategy templates.
5. **Retrieval by causal utility score**  
   Rank memory items by expected decision gain, not embedding similarity alone.

#### Expected outcomes / hypotheses
- Reduction in repeated errors across episodes.
- Improved adaptation speed in novel but related tasks.

---

### 1.4 Integrated Decision Process (RQ4 focus)

#### Identified gaps
1. **Module objective misalignment**  
   Planner, predictor, and memory optimize different losses and communicate through weak interfaces.
2. **No global decision criterion**  
   Combining outputs heuristically causes inconsistent policy choices.
3. **Opaque failure attribution**  
   Hard to diagnose whether failures originate from planning, prediction, memory, or arbitration.

#### Realistic solutions
1. **Unified Decision Score (UDS)**  
   Define a single scoring functional for candidate plans:
   \[
   \text{UDS}(\pi)=w_p S_{plan}(\pi)+w_d S_{pred}(\pi)+w_m S_{mem}(\pi)-w_r \Omega(\pi)
   \]
   where \(\Omega\) captures risk/uncertainty/inconsistency penalties.
2. **Adaptive weighting controller**  
   Learn weights \(w_p,w_d,w_m,w_r\) as a function of context quality (perception confidence, novelty, uncertainty level).
3. **Decision provenance logging**  
   Persist per-step rationale and module-wise contributions for interpretability and diagnostics.

#### Expected outcomes / hypotheses
- Higher reliability than any single module or pairwise combination.
- Clear attribution of gains through ablations and contribution analysis.

---

## 2) Updated Architecture: Cognitive Reliability Loop (CRL)

### 2.1 Core components
1. **Perception & Belief State Builder**  
   Produces object-centric, uncertainty-aware scene graph and latent observation embedding.
2. **Grounded Planner**  
   Generates top-K structured candidate plans in PIR form under skill and constraint grammar.
3. **Predictive Evaluator**  
   Rolls out each candidate with ensemble world model; returns expected utility, uncertainty, consistency score.
4. **Causal Memory & Reflection Unit**  
   Retrieves causally relevant episodes and computes memory support/penalty for each candidate plan.
5. **VLA Policy Adapter (New Integration Layer)**  
   Converts selected structured subgoals into low-level visuomotor policies, while exposing action feasibility logits and intervention hooks back to the planner.
6. **Unified Decision Arbiter**  
   Computes UDS and selects execution prefix (e.g., first N steps), not full rigid sequence, enabling receding-horizon correction.
7. **Execution Monitor & Repair Manager**  
   Detects deviation; triggers local repair or global replanning depending on failure type.

### 2.2 Closed-loop operation
1. Observe and update belief state.
2. Generate candidate PIR plans.
3. Predict outcomes with uncertainty and consistency checks.
4. Retrieve reflective memory and compute causal support.
5. Route top plan prefixes through the VLA Policy Adapter for grounded motor feasibility checks.
6. Score via UDS and execute action prefix with risk-gated VLA control.
7. Compare predicted vs actual outcomes; write reflected memory updates.
8. Repeat until goal completion or termination criteria.

### 2.3 Why this is realistic
- Uses already available ingredients in embodied AI pipelines: scene graphs, LLM planners, world model rollouts, episodic stores.
- Requires integration and objective alignment rather than unrealistic end-to-end perfect models.
- Supports staged implementation and incremental validation.

## 2.4 VLA integration and uncertainty-aware control path (explicit)

To directly address your requirement, the architecture explicitly integrates VLA models *inside* the decision loop (not as an external executor).

### A) VLA integration design
1. **Two-level control contract**  
   High-level planner outputs typed subgoals; VLA adapter translates each subgoal into short-horizon motor primitives.
2. **Bidirectional interface**  
   VLA returns not only candidate actions but also confidence, out-of-distribution (OOD) signals, and intervention requests (e.g., “need closer view”, “grasp uncertain”).
3. **Shared semantic tokens**  
   Use a compact semantic action vocabulary shared by planner and VLA to reduce language-policy mismatch.
4. **Skill fallback switching**  
   If VLA confidence drops below threshold, arbitration can switch to symbolic-safe recovery skills (reposition camera, re-localize object, replan).

### B) Uncertainty-awareness path
1. **Uncertainty Bus (new)**  
   A shared channel aggregates uncertainty from perception, world model ensemble disagreement, memory confidence, and VLA action uncertainty.
2. **Risk budget controller**  
   Each episode receives a risk budget; high-uncertainty actions consume budget faster and trigger conservative behavior when budget is exhausted.
3. **Chance-constrained action selection**  
   Actions are admissible only if estimated failure probability remains below task-specific threshold.
4. **Selective information gathering**  
   Under high uncertainty, the policy can insert sensing actions (viewpoint shift, re-segmentation) before irreversible manipulations.

### Why this is distinct from prior work
- Existing VLA pipelines are commonly reactive end-to-end controllers or loosely coupled with language plans.  
- Existing uncertainty methods often stay within either model-based planning or policy confidence estimation.  
- **This proposal unifies both** by routing uncertainty from *all modules* through one decision interface and by treating VLA as an uncertainty-reporting co-planner at execution time.


---

## 3) Methodology Aligned to RQ1–RQ5

## RQ1: Perception-grounded planning and executability

### Method
- Build two planner variants: baseline unconstrained LLM and constrained PIR planner.
- Introduce grounding validator with affordance and precondition checks.
- Evaluate on tasks with increasing horizon and dependency depth.

### Key metrics
- Structural validity (% plans passing syntax+constraint checks).
- Executability (% steps executable in simulator/robot).
- Goal completion (% successful episodes).
- Replan frequency and average repair depth.

### Gap-to-solution test
- Demonstrate constrained PIR significantly reduces invalid plans and execution dead-ends.

---

## RQ2: Predictive feasibility and model bias

### Method
- Train latent ensemble world model on embodied trajectories.
- Compare single-model vs ensemble feasibility ranking.
- Inject distribution shifts (novel object layouts, distractors, altered dynamics).

### Key metrics
- Plan ranking correlation with actual return.
- Long-horizon prediction error growth curves.
- Calibration metrics (ECE, NLL, Brier).
- Catastrophic failure rate under shift.

### Gap-to-solution test
- Show uncertainty-aware ensemble ranking improves downstream task success and lowers risky plan selection.

---

## RQ3: Memory reflection and credit assignment

### Method
- Implement CEM with contribution tracing and counterfactual updates.
- Compare against vector-similarity episodic retrieval baseline.
- Measure learning across repeated tasks and transfer variants.

### Key metrics
- Repeated-error reduction across episodes.
- Sample efficiency (episodes to reach threshold success).
- Causal retrieval precision (retrieved memory truly relevant to failure mode).
- Memory quality over time (contradiction rate, stale memory usage).

### Gap-to-solution test
- Show causal memory decreases recurrence of same failure patterns and accelerates adaptation.

---

## RQ4: Component contribution and interactions

### Method
- Full factorial ablation:
  - Planner only
  - Planner + predictor
  - Planner + memory
  - Predictor + memory
  - Full CRL
- Analyze interaction effects using ANOVA/mixed-effects models across environments.

### Key metrics
- Success rate, robustness score, and planning efficiency.
- Interaction gain over additive expectation.
- Failure attribution distribution by module.

### Gap-to-solution test
- Demonstrate statistically significant synergistic gains in the full architecture.

---

## RQ5: Uncertainty propagation and reliability

### Method
- Model uncertainty propagation from perception to plan scoring.
- Run stress tests under observation noise, occlusion, delayed actuation, and stochastic dynamics.
- Compare risk-neutral vs risk-aware UDS policies.

### Key metrics
- Reliability under noise (degradation slope).
- Safety violation rate.
- Expected regret vs uncertainty threshold.
- Calibration of risk-aware decisions.

### Gap-to-solution test
- Show risk-aware UDS maintains better reliability/safety trade-off in noisy conditions.

---

## 4) Experimental Program and Implementation Roadmap

### Phase 1: Reproducibility and baseline setup
- Reproduce baseline results for planning, VLA/reactive control, predictive model, and memory reflection modules.
- Standardize task API, logging, seed control, and evaluation scripts.

### Phase 2: Module-wise upgrades
- Integrate PIR planner and grounding checks.
- Add ensemble world model and calibration metrics.
- Implement CEM with contribution and counterfactual updates.

### Phase 3: Unified architecture integration
- Implement UDS arbiter and adaptive weight controller.
- Enable receding-horizon execution and structured repair.

### Phase 4: Robustness and generalization campaigns
- Evaluate in ALFRED/BEHAVIOR-like environments and robotic trajectory datasets.
- Conduct perturbation and partial observability studies.

### Phase 5: Thesis-level analyses
- Statistical significance tests with confidence intervals.
- Error taxonomy and case-study deep dives.
- Compute budget and latency-reliability trade-off profiling.

---

## 5) Evaluation Protocol Details (PhD-level rigor)

1. **Pre-registered hypotheses per RQ** with effect-size targets.
2. **Multi-seed evaluation** (>=5 seeds per condition).
3. **Cross-benchmark validation** to avoid benchmark overfitting.
4. **Out-of-distribution splits** by task composition and object novelty.
5. **Statistical reporting**: confidence intervals, corrected multiple comparisons, robust effect sizes.
6. **Ablation completeness**: include module removal and degraded-module variants.
7. **Reliability diagnostics**: calibration plots, failure mode confusion matrices, uncertainty-performance curves.
8. **Compute accounting**: training/inference cost per module and full system latency.

---

## 6) Anticipated Risks and Mitigation

1. **World model overconfidence**  
   Mitigation: ensemble disagreement thresholding, abstention policy, online recalibration.
2. **Memory drift**  
   Mitigation: confidence decay, periodic contradiction audits, template compression.
3. **Planner bottlenecks with constraints**  
   Mitigation: top-K beam with progressive constraint tightening and cached feasibility checks.
4. **Integration complexity**  
   Mitigation: define strict module contracts (input/output schema) and staged end-to-end CI tests.
5. **Sim-to-real gap**  
   Mitigation: domain randomization, conservative policy constraints, and safety envelope at deployment.

---

## 7) Final Updated Methodology Summary

The updated methodology moves from a loose combination of planning, prediction, and memory toward a **single reliability-centric decision process**. The key contribution is not only improved submodules, but the **coherent coupling** of:
- structured, grounded planning,
- uncertainty-calibrated predictive evaluation,
- causally attributed memory reflection,
- and a unified decision score with adaptive risk sensitivity.

This framework is directly aligned with RQ1–RQ5 and provides a realistic, technically defensible, and experimentally rigorous path to PhD-level contributions in reliable long-horizon embodied decision making, with explicit VLA integration and end-to-end uncertainty-aware arbitration.


## 8) Novelty, non-duplication, and contribution boundaries

To ensure this proposal is new yet implementable, the novelty claim is defined as a **specific combination and interface design**, not as invention of entirely new primitives.

### What is likely already known
- LLM-based task planning exists.
- VLA policies for visuomotor control exist.
- World-model rollouts and memory/reflection methods exist.

### What this proposal contributes that is new (testable claim)
1. **Unified uncertainty bus across planning, prediction, memory, and VLA execution** with one arbitration objective.
2. **Risk-budgeted receding-horizon control** where uncertainty governs when to act, sense, defer, or replan.
3. **Causal memory + counterfactual updates tied to VLA execution outcomes**, not text-only reflections.
4. **Module-contract research artifact** (typed interfaces + provenance logging) enabling reproducible diagnosis of cross-module failures.

### How to keep novelty defensible (important for scholarship review)
- Phrase novelty as “to our knowledge, this integrated formulation and evaluation protocol has not been jointly validated end-to-end.”
- Validate against strong recent baselines and report negative results where integration fails.
- Open-source interfaces, ablation scripts, and uncertainty diagnostics to make claims falsifiable.

### Solvability and feasibility
- The proposal is feasible because each module can be implemented using current SOTA backbones and incrementally integrated.
- The high-risk component is reliable arbitration under compounding uncertainty; this is mitigated by staged milestones and fallback policies.

## 9) Repository-informed integration plan (Plan1/Plan2/VLA/Uncertain2/MemRef/Predictive)

To keep the proposal up-to-date and grounded in your ongoing code history, treat each repository as an evolving module prototype and harmonize them via strict interface contracts.

1. **Plan1 + Plan2 (planning stack)**  
   - Consolidate into one planner API that always emits PIR actions plus precondition/effect tags.  
   - Keep Plan2 as the “constraint-aware” branch and Plan1 as baseline for ablations.
2. **VLA (execution stack)**  
   - Wrap policy outputs with confidence and OOD estimates so the arbiter can perform risk-gated action selection.  
   - Add feedback schema: `exec_status`, `slip_reason`, `motor_confidence`, `needs_reobserve`.
3. **Predictive (world model stack)**  
   - Promote from passive scorer to branch-pruning service during planning search.  
   - Expose calibration diagnostics as first-class outputs for UDS.
4. **MemRef (reflection stack)**  
   - Convert free-form reflections into CEM records with contribution vectors and confidence decay.
5. **Uncertain2 (uncertainty stack)**  
   - Elevate to system-wide Uncertainty Bus aggregator combining perception, prediction, memory, and VLA uncertainty into one normalized reliability state.

### Integration deliverable (new and publishable)
A **cross-repo reliability protocol** (schemas, logging spec, arbitration API, and benchmark scripts) that demonstrates end-to-end uncertainty-aware embodied planning. This protocol itself is a novel research artifact and can support a strong PhD narrative beyond single-model improvements.

## 10) Mathematical formalization of gaps and proposed unique objective

This section adds explicit mathematical representations for the identified gaps and for the proposed architecture-level solution.

### 10.1 Gap formalization (by direction)

Let \(\pi = (a_1,\dots,a_H)\) be a candidate action sequence, \(s_t\) latent world state, \(o_t\) observation, and \(g\) goal.

#### G1: Plan-language / executability mismatch
Define plan plausibility from language model as \(P_{LM}(\pi\mid g,o_{1:t})\), and executable probability under environment dynamics as \(P_{exec}(\pi\mid s_t)\). The mismatch gap is:
\[
\mathcal{G}_{plan}(\pi)=\big|\log P_{LM}(\pi\mid g,o_{1:t})-\log P_{exec}(\pi\mid s_t)\big|.
\]
High \(\mathcal{G}_{plan}\) indicates fluent but non-executable plans.

#### G2: Predictive compounding error
For rollout horizon \(h\), with predictor \(\hat{s}_{t+h}=f_\theta(\hat{s}_{t+h-1},a_{t+h-1})\):
\[
\mathcal{G}_{pred}(h)=\mathbb{E}\big[d(\hat{s}_{t+h},s_{t+h})\big], \quad
\mathcal{G}_{comp}=\sum_{h=1}^{H}\gamma^{h-1}\mathcal{G}_{pred}(h).
\]
Large \(\mathcal{G}_{comp}\) captures long-horizon drift.

#### G3: Memory retrieval non-causality
Let \(m_i\) be memory item \(i\), \(r_i\) its retrieval weight, and \(c_i\) true causal contribution to return delta. Then causal misalignment:
\[
\mathcal{G}_{mem}=\mathbb{E}\left[\sum_i r_i\,|c_i-\hat{c}_i|\right]
+\eta\,\mathrm{KL}(r\,\|\,r^{\star}),
\]
where \(r^{\star}\) is ideal causality-based retrieval.

#### G4: Module objective inconsistency
If modules optimize losses \(\mathcal{L}_{plan},\mathcal{L}_{pred},\mathcal{L}_{mem},\mathcal{L}_{vla}\), inconsistency can be measured by gradient conflict:
\[
\mathcal{G}_{align}=\sum_{i<j}\max\big(0,-\cos(\nabla\mathcal{L}_i,\nabla\mathcal{L}_j)\big).
\]
Higher \(\mathcal{G}_{align}\) means stronger optimization conflict.

#### G5: Uncertainty propagation without control
Let module uncertainties be \(u_t^{perc},u_t^{pred},u_t^{mem},u_t^{vla}\). Uncontrolled propagated uncertainty:
\[
\mathcal{G}_{unc} = \sum_{t=1}^{H}\rho^{t-1}
\Big(u_t^{perc}\oplus u_t^{pred}\oplus u_t^{mem}\oplus u_t^{vla}\Big),
\]
where \(\oplus\) is a normalized uncertainty fusion operator.

---

### 10.2 Proposed new mathematical representation (CRL-UBR objective)

To express novelty clearly, this proposal introduces a new optimization target named:

### **Uncertainty-Budgeted Causal Reliability Objective (UBCRO)**

\[
\pi^{\star}=\arg\max_{\pi\in\Pi}
\underbrace{\mathcal{A}(\pi)}_{\text{goal alignment}}
+\underbrace{\mathcal{F}(\pi)}_{\text{feasibility}}
+\underbrace{\mathcal{C}(\pi)}_{\text{causal memory gain}}
-\underbrace{\lambda_u\mathcal{U}(\pi)}_{\text{integrated uncertainty}}
-\underbrace{\lambda_x\mathcal{X}(\pi)}_{\text{cross-module conflict}}
\]
subject to
\[
\sum_{t=1}^{H} b_t(\pi) \le B,\qquad
\Pr(\text{catastrophic failure}\mid \pi)\le \delta.
\]

Where:
- \(\mathcal{A}(\pi)\): goal-plan semantic alignment score.
- \(\mathcal{F}(\pi)\): world-model expected executable return.
- \(\mathcal{C}(\pi)\): expected causal improvement from memory-guided corrections.
- \(\mathcal{U}(\pi)\): fused uncertainty from perception/prediction/memory/VLA.
- \(\mathcal{X}(\pi)\): module disagreement penalty (planner vs predictor vs VLA vs memory).
- \(B\): episode risk budget; \(b_t\) consumed risk at step \(t\).

This differs from ordinary weighted sums by adding **both** a global uncertainty penalty and a hard budgeted risk constraint tied to receding-horizon execution.

---

### 10.3 Operational decomposition for implementation

At each time \(t\), select short-horizon prefix \(\pi_{t:t+k}\):
\[
\pi_{t:t+k}^{\star}=\arg\max_{\pi_{t:t+k}}
\Big[
\underbrace{S_{plan}}_{PIR}
+\underbrace{S_{pred}}_{ensemble\ rollout}
+\underbrace{S_{mem}}_{causal\ retrieval}
+\underbrace{S_{vla}}_{motor\ feasibility}
-\lambda_u U_{bus}
-\lambda_x D_{mod}
\Big]
\]
subject to
\[
R_{used}(t)+\hat{r}(\pi_{t:t+k})\le B,
\]
where \(U_{bus}\) is the Uncertainty Bus output and \(D_{mod}\) is disagreement across module recommendations.

---

### 10.4 Gap-to-objective linkage (explicit)

The proposed objective directly minimizes all defined gaps:
\[
\min\;\mathbb{E}\left[
\alpha_1\mathcal{G}_{plan}
+\alpha_2\mathcal{G}_{comp}
+\alpha_3\mathcal{G}_{mem}
+\alpha_4\mathcal{G}_{align}
+\alpha_5\mathcal{G}_{unc}
\right]
\]
while maximizing task success and safety under uncertainty constraints.

---

### 10.5 Novelty statement (careful and defensible)

For proposal writing, phrase novelty as:
- “To the best of our knowledge, we are the first to formulate long-horizon embodied planning with a **joint uncertainty budget + causal memory gain + module conflict penalty** under a unified receding-horizon objective.”
- “Even if individual terms exist separately in prior work, their **single constrained formulation and cross-module implementation contract** is the core proposed novelty.”

This wording keeps the claim ambitious but academically defensible.

---

## 11) Practical alignment updates after full cross-check

To improve implementability and scholarship readiness, the following clarifications are now fixed as part of the final plan.

### 11.1 Terminology alignment (UDS vs UBCRO)
- **UBCRO** is the global constrained optimization principle used to define what “reliable” means over an episode.
- **UDS** is the online stepwise scoring proxy used by the arbiter at each receding-horizon decision step.
- Practical rule: UDS must be parameterized so that repeated local choices are consistent with UBCRO constraints (risk budget \(B\), failure bound \(\delta\)).

### 11.2 Minimum viable system (MVS) before full architecture
To reduce integration risk, implement in three practical milestones:
1. **MVS-1 (Planner + Executability checks):** PIR planner + affordance/precondition validator.
2. **MVS-2 (Add Predictor + Uncertainty Bus-lite):** ensemble feasibility ranking + calibration + uncertainty-gated branch pruning.
3. **MVS-3 (Add CEM + VLA Adapter + full Arbiter):** causal memory and bidirectional VLA feedback under UDS/UBCRO constraints.

Each milestone must independently outperform its immediate baseline before proceeding.

### 11.3 Strong baseline policy for fair scholarship evaluation
Use fixed baseline families for all reports:
- baseline A: unconstrained LLM planner,
- baseline B: planner + reactive executor,
- baseline C: planner + predictor (no memory),
- baseline D: planner + memory (no predictor),
- baseline E: full CRL without risk budget,
- baseline F: full CRL + UBCRO constraints (final method).

### 11.4 Resource and feasibility constraints (explicit)
- Report training/inference wall-clock and GPU budget per module.
- Enforce latency cap for real-time arbitration (define max decision latency per step).
- Include a “degraded mode” that falls back to safe recovery skills when compute budget or confidence is insufficient.

### 11.5 Scholarship-facing deliverables
A strong funding-facing package should include:
1. one systems paper (architecture + integration protocol),
2. one methods paper (uncertainty-budgeted arbitration / calibration),
3. one analysis paper (causal memory + failure taxonomy),
4. open-source benchmark harness and logging schema.

### 11.6 Risk governance for real-world relevance
- Add explicit safety-stop triggers under high uncertainty and repeated execution divergence.
- Track and report failure severity tiers (minor recoverable / major unrecoverable / safety-critical).
- Include human-in-the-loop override protocol for high-risk settings.

These updates make the proposal more realistic, staged, and fundable while preserving originality.

---

## 12) Figure plan for Research Design and Methodology (Figure 2)

### Figure title (recommended)
**Figure 2. Cognitive Reliability Loop (CRL) with UBCRO-constrained receding-horizon arbitration.**

### Figure purpose
This figure should serve as the central methodological diagram for the proposal, showing data/control flow and where each mathematical term is computed.

### Figure sketch concept (architecture + loop)
- **Input layer:** goal + multimodal observations.
- **Perception & Belief State Builder:** scene graph + confidence.
- **Grounded Planner (PIR):** top-K structured candidate plans.
- **Predictive Evaluator (ensemble world model):** expected return + epistemic/aleatoric uncertainty.
- **Causal Memory Module (CEM):** retrieval + contribution signal.
- **VLA Policy Adapter:** motor feasibility, OOD/confidence, intervention requests.
- **Uncertainty Bus:** fuses module uncertainties into one reliability state.
- **Unified Decision Arbiter (UDS as online proxy of UBCRO):** selects action prefix under risk budget and chance constraints.
- **Execution Monitor & Repair:** executes, detects divergence, writes back memory updates.
- **Feedback arrows:** close the loop from execution to planner/predictor/memory.

### Mathematical callouts to place on figure
- Near arbiter: \(\pi^*=\arg\max \{S_{plan}+S_{pred}+S_{mem}+S_{vla}-\lambda_uU-\lambda_xD\}\)
- Risk constraint callout: \(\sum_t b_t\le B\), \(\Pr(\text{failure})\le\delta\)
- Uncertainty fusion callout: \(U=\oplus(u^{perc},u^{pred},u^{mem},u^{vla})\)

### Ready-to-use figure generation prompt (for illustrator / diagram tool)
"Create a publication-quality architecture diagram titled 'Cognitive Reliability Loop (CRL) with UBCRO-constrained receding-horizon arbitration'. Show modules: Perception/Belief, PIR Planner, Ensemble Predictor, Causal Memory, VLA Adapter, Uncertainty Bus, Unified Decision Arbiter, Execution Monitor. Include directional arrows and feedback loops. Add equation callouts for objective maximization and risk constraints. Use clean white background, consistent typography, and color-coded uncertainty/safety signals."

### Where to cite Figure 2 in text
Add these two sentences in Methodology narrative:
- **Template sentence 1:** “The full closed-loop decision architecture is shown in **Figure 2**, where planning, prediction, memory, and VLA execution are coordinated through shared uncertainty-aware arbitration.”
- **Template sentence 2:** “As shown in **Figure 2**, UDS operationalizes the UBCRO objective online via receding-horizon selection under explicit risk and failure constraints.”
