---
alias: x-wam
title: Unified 4D World Action Modeling from Video Priors with Asynchronous Denoising
year: "2026"
arxiv_number: "2604.26694"
tags: []
opt_url: https://sharinka0715.github.io/X-WAM/
arxiv_url: https://arxiv.org/abs/2604.26694
code_url: ""
need_revisit: false
---
论文的核心是借助wan2.2同时预测future rgb video, proprios, depth video and action。
![[xwam_fig2.png]]
有一点十分有趣的是他们的delta action、proprios都是SE3的，并且action定义为：
$$FK[target\_action(t+h)]-FK[proprios(a+h)]$$
因此在实际部署过程中需要做累乘的操作——但至少在真机实验上，累积误差非常大——一个更加实际的操作是
1. 直接使用它output的proprios作为action
2. 将action作用于真实的proprios上，不做累积