---
alias: InternVLA-A1.5
title: "InternVLA-A1.5: Unifying Understanding, Latent Foresight,  and Action for Compositional Generalization"
year: "2026"
arxiv_number: "2607.04988"
tags:
  - pre-train
opt_url: https://internrobotics.github.io/internvla-a15.github.io/
arxiv_url: http://arxiv.org/abs/2607.04988
code_url: https://github.com/InternRobotics/InternVLA-A-series
need_revisit: false
---
模仿pi05，然后额外通过可学习的foresight tokens结合frozen的video model预测未来。![[internval-a1.5_fig2.png]]
即使这样，internvla-a1.5也说明pi05的subtask, discrete fast actions, web-data cotrain是有意义的。