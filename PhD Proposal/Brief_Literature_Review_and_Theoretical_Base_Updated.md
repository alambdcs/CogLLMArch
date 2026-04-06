# Updated Brief Literature Review and Theoretical Base
## For: Reliable Long-Horizon Planning in LLM-Based Embodied Agents

Date: April 6, 2026

---

## 1) Brief Literature Review

### 1.1 Historical foundations: symbolic planning and geometric control
Early AI planning established formal action representations and goal-directed reasoning through STRIPS and later PDDL, enabling explicit preconditions, effects, and symbolic search over action spaces (Fikes & Nilsson, 1971; Ghallab et al., 1998; Ghallab et al., 2004). These methods offer interpretability and formal correctness but assume relatively clean symbolic state abstractions and limited uncertainty.

In parallel, motion planning advanced optimality and feasibility in continuous spaces (LaValle, 2006; Karaman & Frazzoli, 2011). These methods are strong in geometry and control but do not directly solve semantic task decomposition, language grounding, or long-horizon causal reasoning.

**Implication for this proposal:** modern embodied intelligence requires both symbolic-level structure and continuous-level execution reliability, motivating hybrid formulations.

---

### 1.2 Learning-based control and predictive world modeling
Reinforcement learning established policy optimization through interaction (Sutton et al., 1999), while model-based variants used learned dynamics to improve data efficiency and long-term reasoning (Ha & Schmidhuber, 2018; Hafner et al., 2020; Hafner et al., 2023). World models are attractive for lookahead planning, but their performance under long-horizon rollout can degrade due to compounding error and representation shift.

**Gap:** existing predictive systems often optimize rollout fidelity but under-report calibration and uncertainty quality as decision-time signals (Guo et al., 2017; Kendall & Gal, 2017).

---

### 1.3 LLM-based planning and embodied language grounding
Large language models demonstrated strong capabilities for instruction following and decomposition (Brown et al., 2020). Embodied extensions (e.g., SayCan-style affordance grounding and PaLM-E) showed that language can improve task-level planning when connected to robot affordances and perception (Ahn et al., 2022; Driess et al., 2023).

However, evidence indicates language coherence does not guarantee executable plans in constrained environments; planner outputs can remain logically plausible while physically invalid (Valmeekam et al., 2023).

**Gap:** token-likelihood optimization is not equivalent to task-success optimization under embodied constraints.

---

### 1.4 Vision-Language-Action (VLA) models and policy transfer
Recent VLA systems (e.g., RT-style and Open X-Embodiment-driven lines of work) show strong progress in mapping multimodal observations to actions at scale (Brohan et al., 2022; Brohan et al., 2023).

These models improve generalization and closed-loop motor behavior, yet many deployments remain reactive or weakly coupled to explicit long-horizon deliberation.

**Gap:** execution policies are often downstream consumers of plans rather than active participants in uncertainty-aware high-level arbitration.

---

### 1.5 Memory and reflective adaptation in agentic systems
Memory-augmented agents introduced reflection and retrieval for iterative performance improvement (Park et al., 2023; Shinn et al., 2023). These approaches improve adaptation, but retrieval is frequently similarity-driven and does not always encode causal responsibility for outcomes.

**Gap:** without causal credit assignment, reflective memory can become noisy, brittle, or weakly actionable in embodied failure recovery.

---

### 1.6 Synthesis of unresolved gaps across directions
Across planning, prediction, VLA execution, and memory, the literature reveals a central systems gap: **modules are strong individually but weakly unified under a shared reliability objective**. The result is inconsistency under long-horizon uncertainty, partial observability, and distribution shift.

This motivates a research program that unifies:
1. structure-aware planning,
2. calibrated predictive feasibility,
3. causal memory reflection,
4. VLA-in-the-loop execution feedback,
5. and uncertainty-budgeted arbitration.

---

## 2) Theoretical Base for the Research

The proposed work is grounded in five complementary theoretical perspectives.

### 2.1 Sequential decision theory under uncertainty
The task is modeled as sequential decision-making with partial observability and stochastic transitions, following the POMDP tradition (Kaelbling et al., 1998). This supports receding-horizon planning, belief updates, and risk-aware action selection over long horizons.

### 2.2 Structured planning theory (symbolic constraints + executable semantics)
From STRIPS/PDDL tradition, we inherit explicit action schemas, precondition-effect reasoning, and compositional plan validity. In this project, these concepts are adapted into a typed Plan Intermediate Representation (PIR) that remains compatible with learned components.

### 2.3 Probabilistic prediction and uncertainty calibration
From model-based RL and probabilistic learning, we adopt learned world dynamics with uncertainty estimation and calibration-aware scoring to avoid overconfident long-horizon errors.

### 2.4 Causal learning and credit assignment
From temporal credit assignment and causal reasoning traditions (Pearl, 2009), memory is treated as a causal intervention aid rather than a semantic cache. Counterfactual analysis and contribution estimation guide what to store, retrieve, and trust.

### 2.5 Hierarchical embodied control with language-policy interfaces
From modern embodied AI, we adopt hierarchical decomposition where language-conditioned high-level plans guide low-level visuomotor policies. The theoretical contribution here is to make this interface bidirectional and uncertainty-aware, so execution feedback reshapes planning in real time.

---

## 3) Positioning the Proposed Contribution

The proposal is intentionally framed as **integration novelty** rather than isolated module novelty:
- not claiming symbolic planning, VLA, world models, or memory are individually new,
- claiming novelty in a unified uncertainty-budgeted, causally reflective, VLA-integrated decision formulation for long-horizon embodied reliability.

This positioning is theoretically grounded, technically implementable, and suitable for PhD-level contributions because it combines formal objectives, measurable hypotheses, and reproducible system contracts.

---

## 4) Suggested citation-ready paragraph for direct use in proposal

Recent embodied AI research has advanced along largely parallel tracks: symbolic planning offers compositional validity but weak robustness to perceptual uncertainty (Fikes & Nilsson, 1971; Ghallab et al., 1998; Ghallab et al., 2004), model-based learning offers predictive lookahead but suffers long-horizon drift and calibration challenges (Ha & Schmidhuber, 2018; Hafner et al., 2020; Hafner et al., 2023), language-grounded planning improves instruction-level decomposition but can fail executability in realistic domains (Ahn et al., 2022; Driess et al., 2023; Valmeekam et al., 2023), VLA models improve sensorimotor policy transfer but are often weakly coupled to explicit deliberative arbitration (Brohan et al., 2022; Brohan et al., 2023), and reflective memory agents improve adaptation yet frequently lack causal credit assignment (Park et al., 2023; Shinn et al., 2023). In addition, uncertainty calibration work suggests confidence quality is essential for safe decision-making (Guo et al., 2017; Kendall & Gal, 2017). Therefore, a unified reliability-centered architecture that jointly optimizes planning structure, predictive uncertainty, causal memory, and VLA execution is a timely and well-grounded research direction.

---

## 5) References (for this section)

- Ahn, M., Brohan, A., Brown, N., Chebotar, Y., Cortes, G., et al. (2022). *Do As I Can, Not As I Say: Grounding Language in Robotic Affordances*. arXiv:2204.01691.
- Brohan, A., et al. (2022). *RT-1: Robotics Transformer for Real-World Control*. arXiv:2212.06817.
- Brohan, A., et al. (2023). *RT-2: Vision-Language-Action Models Transfer Web Knowledge to Robotic Control*. arXiv:2307.15818.
- Brohan, A., Chebotar, Y., Finn, C., Levine, S., et al. (2023). *Open X-Embodiment: Robotic Learning Datasets and RT-X Models*. arXiv:2310.08864.
- Brown, T. B., Mann, B., Ryder, N., et al. (2020). *Language Models are Few-Shot Learners*. NeurIPS.
- Driess, D., Xia, F., Sajjadi, M. S. M., et al. (2023). *PaLM-E: An Embodied Multimodal Language Model*. arXiv:2303.03378.
- Fikes, R. E., & Nilsson, N. J. (1971). *STRIPS: A New Approach to the Application of Theorem Proving to Problem Solving*. Artificial Intelligence.
- Gal, Y., & Ghahramani, Z. (2016). *Dropout as a Bayesian Approximation: Representing Model Uncertainty in Deep Learning*. ICML.
- Ghallab, M., Howe, A., Knoblock, C., et al. (1998). *PDDL—The Planning Domain Definition Language*. AIPS.
- Ghallab, M., Nau, D., & Traverso, P. (2004). *Automated Planning: Theory and Practice*. Morgan Kaufmann.
- Guo, C., Pleiss, G., Sun, Y., & Weinberger, K. Q. (2017). *On Calibration of Modern Neural Networks*. ICML.
- Ha, D., & Schmidhuber, J. (2018). *World Models*. arXiv:1803.10122.
- Hafner, D., Lillicrap, T., Norouzi, M., & Ba, J. (2020). *Dream to Control: Learning Behaviors by Latent Imagination*. ICLR.
- Hafner, D., Pasukonis, J., Ba, J., & Lillicrap, T. (2023). *Mastering Diverse Domains through World Models*. Nature.
- Kaelbling, L. P., Littman, M. L., & Cassandra, A. R. (1998). *Planning and Acting in Partially Observable Stochastic Domains*. Artificial Intelligence.
- Karaman, S., & Frazzoli, E. (2011). *Sampling-based Algorithms for Optimal Motion Planning*. IJRR.
- Kendall, A., & Gal, Y. (2017). *What Uncertainties Do We Need in Bayesian Deep Learning for Computer Vision?* NeurIPS.
- LaValle, S. M. (2006). *Planning Algorithms*. Cambridge University Press.
- Park, J. S., O’Brien, J. C., Cai, C. J., et al. (2023). *Generative Agents: Interactive Simulacra of Human Behavior*. UIST.
- Pearl, J. (2009). *Causality*. Cambridge University Press.
- Shinn, N., Cassano, F., Berman, E., & Labash, A. (2023). *Reflexion: Language Agents with Verbal Reinforcement Learning*. NeurIPS.
- Sutton, R. S., McAllester, D., Singh, S., & Mansour, Y. (1999). *Policy Gradient Methods for Reinforcement Learning with Function Approximation*. NeurIPS.
- Valmeekam, K., et al. (2023). *Large Language Models Still Can’t Plan*. arXiv:2206.10498.
