---
alias: GR-RL
title: "GR-RL: Going Dexterous and Precise for Long-Horizon Robotic Manipulation"
year: "2025"
arxiv_number: "2512.01801"
tags:
  - RL
opt_url: https://seed.bytedance.com/zh/gr_rl
arxiv_url: https://arxiv.org/abs/2512.01801
code_url: ""
need_revisit: false
---
这篇文章其实讲了很多细节内容，之后很多小巧思也被kai0学去了。总之核心在于RL，一个action critic transformer $Q(o_t,l,s_t,a_t)$和对应的noise critic transformer，那么接下来的很多展开都与这个critic相关：
1. RL可以用作data filtering，即剔除那些次优的demonstration数据(sec 3.1)
2. 桥接噪声空间设计(sec 3.3)，即通过critic选择高Q-value的noise
除次之外的mirror左右臂(sec 3.2)其实也被kai0使用了。
![[gr-rl_fig2.png]]