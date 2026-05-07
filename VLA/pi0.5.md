---
alias: pi0.5
title: "pi0.5: a Vision-Language-Action Model with Open-World Generalization"
year: 2025
arxiv_number: "2504.16054"
tags:
  - manipulation
opt_url:
arxiv_url: https://arxiv.org/abs/2504.16054
code_url:
need_revisit: true
---
##### Difference with pi0?
![[pi05.png]]
pi0.5相较于之前pi0的改动，其实也都在上图中的三个阶段（pre-training, post-training and inference）：
1. 增加subtask预测
2. 使用FAST tokenizer encode actions，这一链路上的actions是通过auto-regressive的方式推理。在pre-training, post-training两个阶段中都会作为输出将VLM fine-tuned在robot data数据
3. 增加了如web data等的pretraining数据
4. state改为离散输入，且作为prefix的一部分
5. action expert sampling time $t$ 通过adaRMS的方式注入，即使输入数据在同一个 Transformer Block 内，都受到了完全相同的、由当前时间步 $t$ 决定的全局幅度缩放