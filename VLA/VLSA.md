---
alias: VLSA
title: "VLSA: Vision-Language-Action Models with Plug-and-Play Safety Constraint Layer"
year: 2025
arxiv_number: "2512.11891"
tags:
  - Collision
  - manipulation
opt_url: ""
arxiv_url: https://arxiv.org/pdf/2512.11891
code_url: https://github.com/THU-RCSCT/vlsa-aegis
need_revisit: false
---
![[VLSA_AEGIS_workflow.png]]
做了一个plug-and-play的模块
![[VLSA_pipeline.png]]
本质上就是拿到了最可能产生的object的point cloud，然后使用一个传统的motion planning引导机械臂躲避collision，即classifier guidance.
这篇文章其实也提及一个重要的点（policy能力的上限是base model的上限）：
> Safety-Induced Distribution Shift. We also observe a critical phenomenon where the robot, after successfully avoiding an obstacle, fails to complete the task. This is often caused by distribution shift. To enforce safety, the AEGIS may adjust the trajectory to unfamiliar regions, such as higher altitudes or extreme lateral offsets, that are statistically rare or absent in the training data of the base VLA model. Once forced into these out-of-distribution states, the VLA policy may exhibit erratic behavior, failing to recover towards the goal. This highlights that while the proposed framework ensures safety, the overall task success is ultimately bounded by the generalization capability of the base policy. To mitigate this, future work should involve training VLA models on richer datasets that cover these safety-induced unfamilar regions.
