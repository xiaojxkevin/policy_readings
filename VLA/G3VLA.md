---
alias: G3VLA
title: |-
  G3VLA: Geometric inductive bias for
  Vision-Language-Action Models
year: "2026"
arxiv_number: "2606.24472"
tags:
  - geometry
opt_url: ""
arxiv_url: https://arxiv.org/pdf/2606.24472
code_url: ""
need_revisit: false
---
G3VLA=Ray Embedding+PRoPE+Cross-view Fusion+Geometry Distillation
![[g3vla_overview.png]]
一个可能被问到的问题是：为什么要两步训练，作者的回答是：
> The geometric module is initialized from scratch and receives only a sparse, task-level gradient from the action loss. We therefore introduce a dense auxiliary geometry distillation objective and a two-stage training curriculum to bootstrap the geometric pathway before full policy finetuning

但是非常可惜的是作者大部分实验都在LIBERO上进行的，因此结果并不显著。