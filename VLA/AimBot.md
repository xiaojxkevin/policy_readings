---
alias: AimBot
title: "AimBot: A Simple Auxiliary Visual Cue to Enhance Spatial Awareness of Visuomotor Policies"
year: "2025"
arxiv_number: "2508.08113"
tags:
  - manipulation
opt_url: ""
arxiv_url: https://arxiv.org/abs/2508.08113
code_url: ""
need_revisit: false
---
这篇文章的尝试很有趣！-> 整个过程不需要额外training，只是augment输入
![[aimbot.png]]
一句话讲，就是我们在机械臂末端绑了一个激光笔，我们用这支激光笔显示
1. EE 的指示的方向，与第一个point到的物体
2. EE gripper开合的程度
这就是标题中的auxiliary visual cue。但是这样的方法会有一个需求：depth sensing。这是目前pi0系列并不考虑的因素。同时这篇文章主攻的task是pick-and-place，像叠衣服等长程任务，这种“激光笔式”augmentation不一定是有益的。