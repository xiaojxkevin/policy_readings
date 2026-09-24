---
alias: kai0
title: "χ0: Resource-Aware Robust Manipulation via  Taming Distributional Inconsistencies"
year: 2026
arxiv_number: "2602.09021"
tags:
  - manipulation
opt_url: ""
arxiv_url: https://arxiv.org/abs/2602.09021
code_url: https://github.com/OpenDriveLab/kai0
need_revisit: false
---
一共三个有趣的点：
第一是类似bootstrap aggregating的**Model Arithmetic**:
> Formally, given collected data subsets $\{\mathcal{D}_1, \mathcal{D}_2, \dots, \mathcal{D}_n\}$ sampled from $\mathcal{P}_{\text{train}}$, Model Averaging (MA) trains policies $\{\theta_1, \theta_2, \dots, \theta_n\}$ independently on these subsets and synthesizes their model weights via interpolation:
$$\theta_{\text{merged}} = \sum_{i=1}^{n} \alpha_i \theta_i \quad \text{s.t.} \quad \alpha_i \ge 0, \quad \sum_{i=1}^{n} \alpha_i = 1$$
The weights $\{\alpha_i\}$ are optimized by minimizing a held-out loss over the validation split. Finally, $\theta_{\text{merged}}$ serves as the deployed model $Q_{\text{model}}$.

第二部分是**Stage Advantage**, 其实本质上也是类似pi\*0.6的作法。
第三部分是一些整个的点：
![[kai0_tda.png]]