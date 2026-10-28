---
alias: CR-DAgger
title: "Compliant Residual DAgger: Improving Real-World Contact-Rich Manipulation with Human Corrections"
year: "2025"
arxiv_number: "2506.16685"
tags:
  - human-in-the-loop
opt_url: https://compliant-residual-dagger.github.io/
arxiv_url: https://arxiv.org/pdf/2506.16685
code_url: ""
need_revisit: false
---
不take-over，直接人为上手修复，具体看论文网站下“## Compliant Intervention Interface”那一章的内容。
![[cr-dagger_fig4.png]]
然后两个训练细节：
1. 因为选取的都是recovery阶段的，residual分布很不均匀，因此1）加入以前的无recovery数据并标注成zero-residual；2）直接做demonstration，只是人手一直在辅助
2. 在1的基础上，denser sample only after the human intervention starts
但我认为这篇工作最大的问题是：感觉很难scale up起来