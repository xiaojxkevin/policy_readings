---
alias: GR00T N1
title: "GR00T N1: An Open Foundation Model for Generalist Humanoid Robots"
year: 2025
arxiv_number: "2503.14734"
tags:
  - manipulation
opt_url:
arxiv_url: https://arxiv.org/abs/2503.14734
code_url: https://github.com/NVIDIA/Isaac-GR00T
need_revisit: false
---
在模型层面，它和pi0结构非常像，但是使用的是cross-atten - self-atten - cross-atten - ...的模式：
![[groot_overview.png]]
再从数据维度，它利用了human data和neural data (video generation model generated)。感觉这一点挺有趣的，但是已经有很多论文在向后拓展了。