---
alias: FACT
title: "FACT: Failure-Aware Causal Training for World-Action Models"
year: "2026"
arxiv_number: "2608.10232"
tags:
  - failure-use
opt_url: https://fact-wam.github.io/
arxiv_url: https://arxiv.org/pdf/2608.10232
code_url: ""
need_revisit: false
---
![[fact_fig3.png]]
> For successful demonstrations, actions, future video, and progress are all supervised. For failure rollouts, the action imitation loss is masked, but the observed failed future and lower progress value remain valid supervision

同时作者也提出了一点小track:
**Optional candidate scoring** 是一个 **inference-time best-of-(N) reranking** 机制。推理时 Stage 1 从不同 initial noise 出发，对相同 observation 生成多个 action chunks：  
$$
a^{(k)}=F_\theta(\epsilon_k\mid o,\ell),\qquad \epsilon_k\sim\mathcal N(0,I).
$$
随后将每个生成的 clean candidate (\hat a^{(k)}) 填入训练时 (G) 所占的位置，即 (G\leftarrow\hat a^{(k)})，并在 Stage 2 计算  
$$v^{(k)}=V_\theta(o,\ell,\hat a^{(k)})$$
最终执行  
$$a^\star=\arg\max_k v^{(k)}.  $$
因此 scoring 并不需要生成 future video；其 action-quality 提升来自 value head 对多个 policy samples 的重新排序。Failure-aware training 对这一机制尤其重要，因为它让 value model 真正见过“错误 action ($\rightarrow$) 低 progress / failure”的监督信号，从而能够在 inference 时识别并淘汰较差的 action candidates。