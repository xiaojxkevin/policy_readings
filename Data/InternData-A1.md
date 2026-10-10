---
alias: InternData-A1
title: "InternData-A1: Pioneering High-Fidelity Synthetic Data for Pre-training Generalist Policy"
year: "2025"
arxiv_number: "2511.16651"
tags:
  - pre-train
opt_url: ""
arxiv_url: http://arxiv.org/abs/2511.16651
code_url: ""
need_revisit: false
---
有几点内容觉得值得学习
1. 在训练之前就要大致确认训练的数据格式、比例等等是怎样的，按照论文的结论，trajectory diversity (regarding tasks) 是最重要的一环
2. 提前想好real evaluation的设定，特别是*哪些任务*能够反应*哪些特性*，事实上这一点也需要在数据采集前完成的
3. 如果做sim2real，作者发现相机位置（head + wrist）和 action space representation这两点只要基本一致（作者没有做系统实验论证），就应该能行。