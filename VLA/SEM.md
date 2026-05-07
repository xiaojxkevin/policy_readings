---
alias: SEM
title: "SEM: Enhancing Spatial Understanding for Robust Robot Manipulation"
year: "2025"
arxiv_number: "2505.16196"
tags:
  - manipulation
opt_url: ""
arxiv_url: https://arxiv.org/abs/2505.16196
code_url: ""
need_revisit: false
---
这篇工作更像是3D policy的内容：
$\{I \in \mathbb{R}^{N \times H \times W \times 3}, D \in \mathbb{R}^{N \times H \times W \times 1}, S \in \mathbb{R}^{N_j}, \mathbf{T}\} \cup \{C, E\}$
可以看到输入包括depth images，这其实已经与传统的pi0系列区分开了。
![[sem.png]]
那么其中对我而言比较有趣的是作者引入的Robot State Encoder，即在joint angle space的基础上增加每个joint的SE3 pose。但从它的描述+ablation study (Table 3)，这个state的joint graph attention其实也没有特别大的作用。