# Background and Significance (Rewritten, concise)

Embodied agents increasingly face long-horizon tasks where outcomes are delayed, uncertainty accumulates, and early mistakes cascade into failure. Reliable autonomy therefore requires consistency across decisions, anticipation of downstream effects, and recovery from intermediate errors (Kaelbling et al., 1998; Sutton et al., 1999).

Symbolic planning introduced compositional reasoning, and motion planning ensured geometric feasibility (Fikes & Nilsson, 1971; Ghallab et al., 1998, 2004; LaValle, 2006; Karaman & Frazzoli, 2011). RL and model-based learning added adaptive lookahead (Ha & Schmidhuber, 2018; Hafner et al., 2020).

Recent LLM/VLA advances improved instruction decomposition and embodied execution at scale (Ahn et al., 2022; Driess et al., 2023; Brohan et al., 2022; Brohan et al., 2023).

Yet reliability remains brittle: plans can be coherent but non-executable, prediction errors compound, and memory often lacks causal grounding (Valmeekam et al., 2023; Hafner et al., 2023; Park et al., 2023; Shinn et al., 2023). As shown in Figure 1, these failures compound across planning, prediction, memory, and execution.

Formally, long-horizon error growth can be written as \(E_H=\sum_{t=1}^{H}\gamma^{t-1}\epsilon_t\), showing why local mismatch becomes global failure. Therefore, this proposal develops a Cognitive Reliability Loop with UBCRO, reframing reliability as a coupled systems property under uncertainty.
