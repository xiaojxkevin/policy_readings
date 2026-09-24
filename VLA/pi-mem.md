---
alias: pi-mem
title: |-
  MEM: Multi-Scale Embodied Memory for
  Vision Language Action Models
year: "2026"
arxiv_number: "2603.03596"
tags:
  - memory
opt_url: ""
arxiv_url: https://arxiv.org/pdf/2603.03596
code_url: ""
need_revisit: false
---
文章比pi05好读（可能内容会少一些）。几个核心是：长期记忆使用finetuned languages，短期使用video-like encoder
![[pi06-mem_fig2.png]]
一个非常值得注意的细节是他并没有使用wan结构的video encoder，而是直接改siglip，这样能保持其之前的预训练效果。输入是多帧的 $I_{t-K},\dots,I_t$，每帧仍然独立 patchify，得到$z_{p,t}$ ，其中 (p) 是 spatial patch index，(t) 是时间。
加入固定的 temporal positional embedding: $\hat z_{p,t}=z_{p,t}+e(t)$并设置$e(0)=0$；因此单帧输入时仍保持原 pretrained Image ViT 的行为。
他们的temporal attention 每隔 4 个 ViT layer 加入一次 **causal temporal attention**：
$$z_{p,t}  
\leftarrow  
{z_{p,t-K},\dots,z_{p,t}}$$
Temporal attention 只连接相同 spatial patch index，并复用原 ViT 的 (W_Q,W_K,W_V)。


一个非常有趣的问题是：如果只连接相同的patch，物体如果发生较大（跨patch）移动，它能处理吗？ 其实也是可以的。
物体移动时，其 spatial patch index 会发生变化：
```
t-2: [cup] [   ] [   ]
t-1: [   ] [cup] [   ]
t:   [   ] [   ] [cup]
```
Pi-MEM 依靠 **spatial attention 与 temporal attention 交替执行**传播信息：
$$(t-2,p_1)  
\xrightarrow{spatial}  
(t-2,p_2)  
\xrightarrow{temporal}  
(t-1,p_2)  
\xrightarrow{spatial}  
(t-1,p_3)$$
所以它实现的是高效的 **spatiotemporal feature propagation**，而不是显式 object tracking。
最后，历史帧 tokens 会被丢弃，只保留已经融合历史信息的当前帧 tokens：
$$z_t^{final}=f(I_{t-K})  $$
