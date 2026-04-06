The methodology instantiates a Cognitive Reliability Loop (CRL) under UBCRO. Given goal \(g\) and observations \(o_{1:t}\), a constrained PIR planner generates candidates by
\[\pi\sim P_{\theta}(\pi\mid g,o_{1:t}),\]
subject to precondition/affordance validity checks.

An ensemble world model evaluates feasibility through rollout
\[\hat{s}_{t+h}=f_{\theta}(\hat{s}_{t+h-1},a_{t+h-1}),\quad
S_{pred}(\pi)=\mathbb{E}[R(\pi)]-\lambda_e\sum_{h=1}^{H}\gamma^{h-1}d(\hat{s}_{t+h},s_{t+h}).\]

Causal Episodic Memory contributes
\[S_{mem}(\pi)=\sum_i r_i(\pi)\,c_i,\]
where \(r_i\) is retrieval weight and \(c_i\) is estimated causal contribution. VLA feasibility returns \(S_{vla}(\pi)\). Module uncertainty is fused by
\[U=\oplus(u^{perc},u^{pred},u^{mem},u^{vla}).\]

Online arbitration uses
\[\text{UDS}(\pi)=S_{plan}(\pi)+S_{pred}(\pi)+S_{mem}(\pi)+S_{vla}(\pi)-\lambda_uU-\lambda_xD,\]
with disagreement penalty \(D\). Episode-level control follows
\[\pi^*=\arg\max_{\pi}\text{UDS}(\pi),\quad \sum_t b_t\le B,\quad \Pr(\text{failure}\mid\pi)\le\delta.\]

Implementation is staged: MVS-1 (PIR+executability), MVS-2 (ensemble prediction+calibration), MVS-3 (CEM+VLA+full arbitration). Evaluation maps to RQ1–RQ5 with multi-seed/OOD ablations and metrics for executability, calibration (ECE/NLL/Brier), repeated-failure reduction, and safety-critical failure rate.
