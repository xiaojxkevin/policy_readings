---
alias: REMAC
title: REAL-TIME ROBOT EXECUTION WITH MASKED ACTION CHUNKING
year: "2026"
arxiv_number: "2601.20130"
tags:
  - Inference
opt_url: https://openreview.net/forum?id=r0RGJ1j9on
arxiv_url: https://arxiv.org/abs/2601.20130
code_url: https://github.com/hatchetProject/REMAC
need_revisit: true
---
###### Training
Some notations: 
- **$A$ (`action_curr`)**: The ground-truth expert action chunk directly from the dataset.
- **$\tilde{A}$ (`action_pred_curr`)**: The action chunk generated completely from scratch by the **frozen, pretrained backbone** (without LoRA).
- **$\hat{A}$ (`action_init`)**: The mixed action chunk used for the "Self-conditioned Curriculum" $\gamma \sim \mathrm{Bernoulli}(\sigma),\ \sigma \in [0,1].\ \hat{\mathbf{A}}_t = \gamma \mathbf{A}_t + \text{stop-gradient}\left((1-\gamma)\tilde{\mathbf{A}}_t\right)$.
- **$u$ (`u_t_curr`)**: The ground-truth flow target. Mathematically, it is simply the vector pointing from the sampled noise to the target action.
- **$\hat{u}$ (`pred_curr`)**: The flow predicted by the **new, LoRA-adapted policy** ($\hat{v}_\pi$).
- **$\tilde{u}$ (`with_backbone`)**: The flow predicted by the **frozen, pretrained backbone** ($v_\pi$).
![[remac_train.png]]
我个人认为这里的loss完全多余因为$\tilde{\mathbf{u}}_\tau$会被约掉$\mathcal{L}_\Delta = \sum_d \frac{ \sum_{\tau=0}^{P-1} \left\| m_d^\tau ( \mathbf{u}_\tau - \tilde{\mathbf{u}}_\tau ) - m_d^\tau ( \hat{\mathbf{u}}_\tau - \tilde{\mathbf{u}}_\tau ) \right\|_2^2 }{ \max\left( 1, \sum_{\tau=0}^{P-1} m_d^\tau \right) }$。
一个值得学习的点在于这个delay d会被
###### Inference
$\mathbf{A}_{t}^{\tau + \frac{1}{n}} = \mathbf{m} \odot \left( \mathbf{A}_{t}^{\tau} + \frac{1}{n} \hat{\mathbf{V}}_{\pi} \left( \mathbf{A}_{t}^{\tau}, \mathbf{O}_{t}, \tau \right) \right) + (1 - \mathbf{m}) \odot \mathbf{A}_{t}^{p}$.