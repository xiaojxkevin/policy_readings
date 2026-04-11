---
alias: pi0
title: "pi0: A Vision-Language-Action Flow Model for General Robot Control"
year: 2024
arxiv_number: "2410.24164"
tags: []
opt_url:
arxiv_url: https://arxiv.org/abs/2410.24164
code_url:
need_revisit: true
---

# pi0

## High-Idea

把 VLM 的语义能力和 flow matching action generation 结合起来，做更通用、更灵活的 general robot control，而不是继续停留在 tokenized action 的范式里。

## Tricks

- action head 走 flow matching，而不是离散 token prediction。
- 覆盖多种机器人形态和任务，强调 generalist robot policy。
- 支持 zero-shot、language following 和 finetune acquisition 等多种使用方式。

## My Questions

- flow matching 在 robot action 上相对 diffusion / autoregressive 的核心优势到底是什么？
- `pi0` 的 gains 主要来自 backbone 语义能力，还是来自 action generation 方式？
- 它对 control frequency、chunk size、latency 的要求有多强？
