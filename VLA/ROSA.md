---
alias: ROSA
title: "ROSA: Harnessing Robot States for Vision-Language and Action Alignment"
year: "2025"
arxiv_number: "2506.13679"
tags:
  - manipulation
opt_url: https://openreview.net/forum?id=I8a6bc9rmz
arxiv_url: https://arxiv.org/abs/2506.13679
code_url: ""
need_revisit: false
---
这篇文章感觉也是挺有趣的，他们的一大动机是发现从VLM到VLA存在空间上（即语言、图片到action的语义不同）和时间上（预测未来）的gap：
![[rosa_motivation.png]]
那么作者的解决方案是引入“state estimation”这个补充目标进行训练的：
![[rosa_model.png]]
这里会有很多的问题：
1. 作者的框架还是基于OpenVLA一套的，没有额外的action expert，其实我个人认为diffusion/flow matching本身就在尝试解决temporal gap。作者没有添加action expert其实也是希望input-output是统一起来的，即在不改变网络结构的前提下也可以同时训练next-action + current state estimation
2. 最奇怪的一点还是在作者并没有将state作为predict next action的输入，这就十分奇怪了，因为明明它是比较容易获取的data。只能说为了“预测state”作者舍弃了很多。
###### Takeaway
>Using larger amounts of state data degrades performance, likely due to distributional shifts that impair the model’s ability to predict future actions

