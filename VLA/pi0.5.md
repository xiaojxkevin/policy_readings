---
alias: pi0.5
title: "pi0.5: a Vision-Language-Action Model with Open-World Generalization"
year: 2025
arxiv_number: "2504.16054"
tags: []
opt_url:
arxiv_url: https://arxiv.org/abs/2504.16054
code_url:
need_revisit: true
---

# pi0.5

## High-Idea

在 `pi0` 基础上继续往 open-world generalization 推进，不只学低层动作，还通过 heterogeneous co-training 把高层语义、子任务预测和多源数据一起揉进 policy。

## Tricks

- heterogeneous co-training，把多机器人、多任务、高层语义监督和 web 数据联合起来。
- hybrid multimodal examples，同时包含图像、语言、检测、subtask 和 action。
- 明确面向 long-horizon、真实家庭场景的泛化，而不是只做 benchmark 内泛化。

## My Questions

- 这些 heterogeneous signals 分别贡献了多少，是否存在关键 supervision bottleneck？
- `pi0.5` 更像单体 end-to-end policy，还是隐式分层系统？
- 如果没有大量异构数据，`pi0.5` 的 recipe 还能保留多少收益？
