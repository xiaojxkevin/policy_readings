---
alias: Fast-dVLA
title: "Fast-dVLA: Accelerating Discrete Diffusion VLA to Real-Time Performance"
year: "2026"
arxiv_number: "2603.25661"
tags:
  - Inference
opt_url: ""
arxiv_url: https://arxiv.org/abs/2603.25661
code_url: ""
need_revisit: false
---
论文的前提是discrete diffusion，这在pi05等大众化的policy中并不常见。
一个简单的表述是：action token依然离散化，但也在vocab中，且使用cross-entropy计算，大致的过程是：
```
t = 0
A0       [B, L]
████████████████        almost all clean

t = 0.3
A_t      [B, L]
██░████░░██████░        select ones with high prob

t = 0.7
A_t      [B, L]
░░░█░░░░██░░░░░

t = 1
A_T      [B, L]
░░░░░░░░░░░░░░░

█ = clean action token
░ = MASK
```
作者发现的一个规律是即使是bidirectional的全局去噪，在靠前位置的action依然可以被更快denoise出来。因此可以对action tokens划分block，和AR类似，后面的block看不到前面的block。![[fast-dvla_fig5.png]]
因此最后的流程大概是：
![[fast-dvla_explain.png]]
