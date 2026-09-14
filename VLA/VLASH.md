---
alias: VLASH
title: "VLASH: Real-Time VLAs via Future-State-Aware Asynchronous Inference"
year: "2025"
arxiv_number: "2512.01031"
tags:
  - Inference
opt_url: ""
arxiv_url: https://arxiv.org/abs/2512.01031
code_url: https://github.com/mit-han-lab/vlash
need_revisit: false
---
这个overview挺有趣的：
![[vlash_fig3.png]]
但是这也引出了一个新的问题：我们应该如何进行训练？training-time RTC可以并行化的原因是它只修改attention mask，但这篇论文需要对数据进行扩充：
对每个原始样本    
$(O_t,; S_t,; A_{t+k},; L)$
构造多个 temporal offset：  
$(O_t,; S_{t+\delta},; A_{t+\delta+\delta+k},; L),$  
$\quad \delta=0,\dots,\Delta_{\max}.$  
 训练时 ($S_{t+\delta}$) 直接取 demonstration 中的真实 future state。
同一个 (O_t,L) 只编码一次，并共享给所有 offset branch：
$[O_t,L,;(S_t,A_t),;(S_{t+1},A_{t+1}),\dots]$  
不同 offset branch：
- 都能 attend 到 shared observation；
- 不能彼此 cross-attend；
- 使用相同的 positional indices。
因此若 physical batch size 为 (N)，则  
$B_{\text{effective}} = N(\Delta_{\max}+1).$  

> 本质上：用同一张“旧”图像配不同 future state / future action，迫使模型真正利用 robot state，并以共享视觉计算的方式高效实现多 offset 训练。

