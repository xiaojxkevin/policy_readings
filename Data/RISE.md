---
alias: RISE
title: "RISE: Self-Improving Robot Policy with Compositional World Model"
year: "2026"
arxiv_number: "2602.11075"
tags:
  - RL
opt_url: https://opendrivelab.com/RISE
arxiv_url: https://arxiv.org/abs/2602.11075
code_url: ""
need_revisit: false
---
以下的这张图能非常好说明RISE和其他工作的区别
![[rise_related_work.png]]
具体的算法可以理解为：
```txt
使用真实数据：
    训练 Dynamics Model
    训练 Progress + TD Value Model
    warm-up advantage-conditioned policy

重复：
    从真实离线数据取初始状态 o

    要求当前 rollout policy 在最高 advantage 条件下提出动作 a

    Dynamics Model：
        根据 o 和 a 生成未来多视角画面 ô

    Value Model：
        比较 V(ô) 与 V(o)
        计算实际 advantage Â
        将 Â 离散为 bin ẑ

    保存 imagined sample：
        (o, a, ẑ)

    最多把 ô 再作为下一轮输入一次

    使用：
        imagined samples + real offline samples
    更新 behavior policy

    用 EMA 将 behavior policy 更新到 rollout policy
```
==但是我有一个问题：为什么RISE会比RECAP更好？==
现在来看不好说，因为这里的RECAP baseline很可能没有迭代式增加数据。