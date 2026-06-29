---
alias: PLD
title: SELF-IMPROVING VISION-LANGUAGE-ACTION MODELS WITH DATA GENERATION VIA RESIDUAL RL
year: "2026"
arxiv_number: "2511.00091"
tags:
  - RL
opt_url: https://wenlixiao.com/self-improve-VLA-PLD
arxiv_url: https://arxiv.org/abs/2511.00091
code_url: ""
need_revisit: false
---
论文的三那个阶段都展示在以下的algo中
![[PLD_algo.png]]
需要注意的点是environment step返回的reward，real world考虑训练一个binary reward classifier：输入是相机图像和 proprioception，输出当前状态是否成功。论文 appendix 里说：
- 每个任务训练一个独立 classifier
- 数据是小规模 success/failure states
- 模型结构是 pretrained ResNet-10 + 3-layer MLP
- 还用 augmented false-positive samples 训练到约 99% success-rate
- GPU 插拔长程任务里，reward classifier 还用于控制 stage/state machine 的切换
#### Notes
reward是0,1分布的，需要训练一个binary classifier，它的输入就是policy的输入（不包含prompt）。但是这里可能有一个问题是，如果supervised训练，标注从哪里来呢？
DeepSeek通读论文后给出了一个我认为还可以的理解：
- pick up blue cube：方块是否已经被稳定抓起，离开桌面，并且目标物体正确。
- pick-and-place：物体是否在目标区域内，机器人是否释放或稳定放置。
- peg insertion：peg 是否插入孔中到足够深度，姿态是否正确。
- drawer opening：抽屉是否打开到目标程度。
- GPU insertion：是否完成当前子阶段，比如插入 slot 1、移到 slot 3、稳固插入等。
人标的不是“这一步动作好不好”，而是“这个状态是否已经算完成任务”。

### My concern
作者其实也有比较long-horizon tasks，但是得出的结论是：the fine-tuning of the data PLD exceeds the tuning of the data from the baseline policy roll-out (self-bootstrapped), but still falls short of the performance achieved with demonstrations by human experts
因此从这个角度看，简单的RL进行修正并不能完全解决问题，且DAgger还是针对long-horizon任务的一个非常有效的数据采集手段。
