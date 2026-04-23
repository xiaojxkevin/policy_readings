---
alias: pi0
title: "pi0: A Vision-Language-Action Flow Model for General Robot Control"
year: 2024
arxiv_number: "2410.24164"
tags:
  - manipulation
opt_url: https://www.pi.website/
arxiv_url: https://arxiv.org/abs/2410.24164
code_url: https://github.com/Physical-Intelligence/openpi
need_revisit: false
---
###### Overview
![[pi0_overview.png]]
pi0基本确立了之后VLA的几个重要的内容：
1. pre-training  + post training两个阶段训练的scheme
2. 确立action expert的flow matching/diffusion process for **continuous** action outputs.
3. 论文证明pre-training有效果
4. 论文也论证了引入VLM从而有的“language instructions following”的特性，这也应该是为之后的subtask的生成做准备。
###### Pipeline
![[pi0.drawio.svg]]
具体的attention mask为：

|        | Prefix | State | Action |
|:------:|:------:|:-----:|:------:|
| Prefix |    1   |   0   |    0   |
|  State |    1   |   1   |    0   |
| Action |    1   |   1   |    1   |

