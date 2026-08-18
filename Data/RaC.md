---
alias: RaC
title: "RaC: Robot Learning for Long-Horizon Tasks by Scaling Recovery and Correction"
year: "2025"
arxiv_number: "2509.07953"
tags:
  - human-in-the-loop
opt_url: https://rac-scaling-robot.github.io/
arxiv_url: https://arxiv.org/abs/2509.07953
code_url: ""
need_revisit: false
---
![[rac_overview.png]]
本质上就是多了一段“回到机械臂的in-distribution区域”，那么需要明确的是：“什么是in-distribution”的数据？
作者做了一些可视化的方法：
![[rac_fig5.png]]
还有几点：
1. 对于dagger类型的，作者刻意“Terminate after intervention. After an intervention concludes, we terminate the entire episode.” 因此需要多次迭代采集更新。因此later的subtask会在之后的轮数中被采集到。
2. 每次采集数据后，会将所有数据做并集，==这里推测每次迭代的policy都是从base ckpt开始训练，而非从上一轮的policy==