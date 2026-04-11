---
alias: SmolVLA
title: "SmolVLA: A Vision-Language-Action Model for Affordable and Efficient Robotics"
year: 2025
arxiv_number: "2506.01844"
tags:
  - manipulation
  - arm
opt_url:
arxiv_url: https://arxiv.org/abs/2506.01844
code_url: https://huggingface.co/blog/smolvla
need_revisit: false
---

![[smolvla.png]]
作者们的motivation就是去造一个efficient的VLA，本质上完成的内容还是
1. 一个轻量化推理的VLM，且只用其中的一半的: "In practice, we find setting N to half the total layers (N = L/2) offers a good tradeoff between speed and performance, effectively halving the LLM and action expert’s computational cost."
2. self-attention和cross-attention交替进行的flow-matching
3. async inference，感觉可以用RTC取代掉
但是有一点值得学习的是他们对于async action buffer的可视化，感觉之后可以用在我们的研究上。
![[smolvla_async.png]]
