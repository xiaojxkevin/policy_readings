---
alias: EgoMimic
title: "EgoMimic: Scaling Imitation Learning via Egocentric Video"
year: "2024"
arxiv_number: "2410.24221"
tags:
  - human-data
opt_url: https://egomimic.github.io/
arxiv_url: https://arxiv.org/abs/2410.24221
code_url: ""
need_revisit: false
---
1. 对每个action chunk，都转换到chunk对应的head frame下，而非用SLAM create出的第一帧作为world/fixed frame
2. 对ego数据和真机数据都做gaussian normalization
3. mask ego数据中手、手臂等，改成红线
4. 训练时joint and ee pose一起训练，最终推理时使用joint进行控制