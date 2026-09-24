---
alias: openvla-oft
title: "Fine-Tuning Vision-Language-Action Models:  Optimizing Speed and Success"
year: "2025"
arxiv_number: "2502.19645"
tags:
  - manipulation
opt_url: ""
arxiv_url: https://arxiv.org/abs/2502.19645
code_url: ""
need_revisit: false
---
文章的结论非常清晰：
> Our study reveals several key insights that build on each other: (1) parallel decoding with action chunking not only boosts inference efficiency but also improves success rates on downstream tasks while enabling greater flexibility in the model’s input-output specifications; (2) continuous action representations further improve model quality compared to discrete representations; and (3) fine-tuning the VLA with an L1 regression objective yields comparable performance to diffusion-based fine-tuning while offering faster training convergence and inference speed.

其余一个值得记忆的点是作者使用FiLM将language embeddings 投影到 visual去，这是因为“During training, policies may learn to latch onto such spurious correlations when predicting actions, rather than properly attending to the language instructions, resulting in poor adherence to the user’s commands at test time”。