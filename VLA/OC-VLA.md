---
alias: OC-VLA
title: "Grounding Actions in Camera Space: Observation-Centric Vision-Language-Action Policy"
year: "2025"
arxiv_number: "2508.13103"
tags:
  - manipulation
opt_url: ""
arxiv_url: https://arxiv.org/abs/2508.13103
code_url: ""
need_revisit: false
---
1. 论文使用EE pose进行控制，即监督信号（输出）都是在camera space
2. 论文全文都使用third-view camera，没有第一视角的相机。作者感觉在处理“相机位置改变”从而导致“世界系发生改变的”问题。
3. 作者做了一个很有趣的实验，他们随意更换camera的位置进行zero-shot的测试。结果显示不仅在cam坐标系内效果好，甚至具备一定的zero-shot能力。
	![[oc-vla.png]]
4. 但是这篇论文其实也有几个问题，比如hand-eye calibration error没有分析（因为cam base and robot base本质上就差了一个SE3，理论上policy应该能学会的，为什么乘了一个旋转就能解决这个问题？）等