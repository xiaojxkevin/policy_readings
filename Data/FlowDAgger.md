---
alias: FlowDAgger
title: |-
  FlowDAgger: Human-in-the-Loop Adaptation of
  Generative Robot Policies in Latent Space
year: "2026"
arxiv_number: "2607.08877"
tags: []
opt_url: ""
arxiv_url: https://arxiv.org/pdf/2607.08877
code_url: ""
need_revisit: false
---
文章并没有说改变DAgger的采集方式，而是利用方式。一个最常见的利用方式就是将DAgger和一般的demonstration等融合起来训练，然后作者提出：能不能训练一个noise policy学习DAgger+同等比例的success rollout以获得更好initial noise？
那么就需要一个flow matching去噪的逆变换，作者使用fixed-point method
$$
\begin{align*}
x_9^{(0)} &= x_{10} - \Delta t \cdot v(x_{10}) \\
x_9^{(1)} &= x_{10} - \Delta t \cdot v(x_9^{(0)}) \\
x_9^{(2)} &= x_{10} - \Delta t \cdot v(x_9^{(1)}) \\
x_9^{(M)} &= x_{10} - \Delta t \cdot v(x_9^{(M-1)})
\end{align*}
$$
使用逆变换可以获得DAgger and success rollouts对应的noise，然后可以学习一个mapping就OK了。但是作者并没有讨论上述两种动作分布initial noise的区别（且我理解论文中都是单任务训练），很难说这个noise policy真的有什么作用。我个人认为GR-RL的RL方式可能会更好一点。