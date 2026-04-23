---
alias: pi-FAST
title: "FAST: Efficient Action Tokenization for Vision-Language-Action Models"
year: "2025"
arxiv_number: "2501.09747"
tags:
  - manipulation
opt_url: ""
arxiv_url: https://arxiv.org/pdf/2501.09747
code_url: ""
need_revisit: false
---
这是一个即插即用的tokenizer（利用预训练LLM的autoregressive能力），核心在于将continuous **actions** (joint space, EE pose in base frame, EE pose in cam frame)转化为discrete tokens.
![[fast_overview.png]]
特别的，这里的chunk 长度一直为one second，且作者使用各种control frequency的action作为输入。因为他们将时域信号转成频域信号，那么对于一个chunk而言，低频率信号/能量为主，因此其实输入是统一的。
###### Why actions, not state?
> Note that a simple bin tokenization scheme is sufficient for the proprioceptive state, since it is an input to the policy (as opposed to the action outputs, that require advanced tokenization as our experiments demonstrate).
##### Usage
这个tokenization可以直接用在像pi0-FAST和OpenVLA模型上，本质上就是只使用finetuned VLM auto-regressive actions。为避免语义混乱，可使用least used 1024 tokens当成action tokens。

考虑一个实际的使用场景：假设我们使用agilex piper dual arm system，控制频率为30Hz，但是我们目前的输出长度为50 steps。这样的一个设计其实与论文中的*one second*其实有些矛盾。目前可能的解决方法是：
1. 将待生成的action chunk长度改为control frequency；
2. 根据特定的任务自己训练一个specific tokenizer