---
alias: RoboVIP
title: "RoboVIP: Multi-View Video Generation with Visual Identity Prompting Augments Robot Manipulation"
year: "2026"
arxiv_number: "2601.05241"
tags:
  - Aug
opt_url: https://github.com/RoboVIP/RoboVIP_VDM
arxiv_url: https://arxiv.org/pdf/2601.05241
code_url: ""
need_revisit: false
---
![[robovip_overview.png]]
动作轨迹没有改变，只是针对1) temporal consistency; 2) multi-view consistency 两个层面进行的数据增强。增强只是针对背景和无关物体，不改变机械臂和对应的交互物体。
![[robovip_arch.png]]
文中一大核心的"visual identity prompting"就是给【scene setup】中的语言描述提供对应的视觉特征的。