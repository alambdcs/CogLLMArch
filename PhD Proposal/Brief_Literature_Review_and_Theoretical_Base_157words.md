Symbolic planning contributed compositional reasoning, and motion planning ensured feasibility (Fikes & Nilsson, 1971; Ghallab et al., 1998; LaValle, 2006). In embodied settings, language-based planners can still output physically ungrounded steps (Valmeekam et al., 2023).

RL and world models enabled lookahead (Sutton et al., 1999; Ha & Schmidhuber, 2018; Hafner et al., 2020, 2023), yet rollout error accumulates as \(E_H=\sum_{t=1}^{H}\gamma^{t-1}\epsilon_t\), and weak calibration limits trust (Guo et al., 2017; Kendall & Gal, 2017).

VLA systems scaled execution (Ahn et al., 2022; Driess et al., 2023; Brohan et al., 2023), but often remain weakly coupled to uncertainty-aware long-horizon arbitration.

Reflective memory helps adaptation (Park et al., 2023; Shinn et al., 2023), though retrieval is frequently similarity-based rather than causal (Pearl, 2009).

Under a POMDP perspective (Kaelbling et al., 1998), these limits motivate CRL+UBCRO as a unified reliability formulation.
