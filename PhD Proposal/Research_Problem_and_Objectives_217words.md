Embodied agents still fail on long-horizon tasks because planning, prediction, memory, and execution are optimized separately. This fragmentation yields non-executable plans, drifting feasibility estimates, weak recovery, and repeated errors, especially when memory is not causally grounded and arbitration is not uncertainty- and risk-aware. The core problem is: **how can an agent generate, evaluate, execute, and refine actions within a consistent, uncertainty-aware decision process?**

This proposal addresses that gap through CRL and an explicit unified decision formulation:
\[\pi^*=\arg\max_{\pi}\big(\mathcal{A}(\pi)+\mathcal{F}(\pi)+\mathcal{C}(\pi)-\lambda_u\mathcal{U}(\pi)-\lambda_x\mathcal{X}(\pi)\big),\;\text{s.t. }\sum_t b_t\le B,\;\Pr(\text{failure}|\pi)\le\delta.\]

**RQ1:** Does perception-grounded planning improve executability? **RQ2:** Do calibrated world models improve long-horizon ranking? **RQ3:** Does causal episodic memory reduce repeated failures? **RQ4:** What are independent and interaction effects of planning, prediction, memory, and VLA integration? **RQ5:** How does uncertainty propagation affect reliability and safety?

Objectives: build a constrained PIR planner, calibrated ensemble prediction, causal memory with counterfactual reflection, uncertainty- and risk-aware VLA arbitration, and constrained receding-horizon optimization. Hypotheses H1–H5 test executability, ranking quality, repeated-error reduction, integration gains, and catastrophic-failure reduction.
