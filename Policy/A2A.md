---
alias: A2A
title: Action-to-Action Flow Matching
year: "2026"
arxiv_number: "2602.07322"
tags:
  - Inference
opt_url: https://lorenzo-0-0.github.io/A2A_Flow_Matching
arxiv_url: https://arxiv.org/pdf/2602.07322
code_url: https://github.com/JIAjindou/A2A_Flow_Matching
need_revisit: false
---
核心是使用previous action chunks作为下一步chunk的condition（在latent space）
![[a2a_overview.png]]
一个值得进一步思考的问题是：既然在 RTC（real-time chunking）这类 inference-time 方法中，利用 previous actions 来约束或 warm-start 后续 action generation 已经是相当自然的思路，那么为什么 π 系列仍然选择从 Gaussian noise 出发，而没有进一步采用 A2A 这种以 previous-action history 作为 flow source distribution 的设计？一种可能的解释是，这种较强的 temporal prior 在单任务或相对同质的轨迹分布上非常有效，但其可扩展性尚不明确。A2A 的 generalization 实验实际上暴露了这一潜在问题：当 previous-action distribution 因初始状态变化而偏离训练分布时，性能会明显下降，而作者需要通过向 history actions 注入 noise 来增强鲁棒性。这说明 previous-action initialization 在提高 sample efficiency 和降低视觉依赖的同时，也可能使模型更加依赖训练数据中的 trajectory manifold。更关键的是，目前这些 generalization 实验主要是在单一任务上完成的，因此尚不能判断当模型扩展到大规模 multi-task、multi-embodiment pretraining 时，这种依赖会如何变化。随着任务和运动模式的多样性增加，同一个 previous-action history 可能对应更多不同的 future trajectories，从而削弱 past–future distribution proximity 这一 A2A 的核心优势。因此，一个重要的开放问题是：previous-action-conditioned flow 是否能够保持其在单任务 setting 中观察到的简单 transport geometry，还是需要引入更弱或自适应的 history prior，才能真正适用于 π-style 的 large-scale generalist policy。
![[a2a_fig9.png]]