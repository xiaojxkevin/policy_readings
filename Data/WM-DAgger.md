---
alias: WM-DAgger
title: "WM-DAgger: Enabling Efficient Data Aggregation for Imitation  Learning with World Models"
year: "2026"
arxiv_number: "2604.11351"
tags:
  - Aug
opt_url: ""
arxiv_url: https://arxiv.org/abs/2604.11351
code_url: ""
need_revisit: false
---
这篇论文的核心是使用world-model在少量采集的数据上扩充新的训练数据。那这必然需要考虑几件事：
1. 数据的质量上限完全取决于world-model本身
2. 即便有了一个较为强大的world-model，我们依然需要考虑1）action本身的质量；2）图片/视频生成的质量。
那么对于action本身，作者采用的方式是在demonstration data基础上增加扰动和恢复(扰动的反方向)动作，因此虽然这些扰动不一定完全符合真实OOD情况，但至少是正确的。
![[WM-DAgger-action.png]]
但是对剩下的image/video generation，作者除了比较DINO的feature(我目前感觉也十分不合理)，和少量的post-training数据，剩下的过程就基本完全依赖原身cosmos-predict的能力了。因此我感觉这一部分作者实际上并没有说明清楚。