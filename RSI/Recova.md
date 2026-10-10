---
alias: Recova
title: "Recova: Agent-Guided Failure Recovery for Autonomous Robotic Manipulation"
year: "2026"
arxiv_number: "2610.01178"
tags:
  - real2sim2real
  - human-in-the-loop
opt_url: https://www.liuisabella.com/Recova
arxiv_url: https://arxiv.org/pdf/2610.01178
code_url: ""
need_revisit: false
---
![[recova_pipeline.png]]
论文主要是一个仿真环境 + DAgger rollout + policy and recovery双系统的架构。这里我们主要抓住几个问题：
1. 这里的digital twin的意义是什么？
2. Agent在哪里被使用了？

首先第一个问题。digital twin首先是用于base policy and recovery skills的初始化；其次是，在人为intervention后，可以将这个新的skill放到仿真中获得大量数据进行训练。

对于第二个问题，agent的主要内容是digital twin的搭建和真机中的monitor:
![[recova_prompt.png]]