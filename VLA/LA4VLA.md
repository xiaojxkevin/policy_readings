---
alias: LA4VLA
title: |-
  LA4VLA: Learning to Act without Seeing
  via Language-Action Pretraining
year: "2026"
arxiv_number: "2606.27295"
tags:
  - atomic
opt_url: ""
arxiv_url: https://arxiv.org/pdf/2606.27295
code_url: https://github.com/MINT-SJTU/LA4VLA
need_revisit: false
---
一般来讲，VLA模型学习的是某一种mapping:
$$f(l_t,o_t,s_t) \rightarrow A_{t:t+H} $$
这篇文章讨论想思考的一个问题是：对于一些“基础”的运动，比如将机械臂朝上下左右等axis-aligned的运动，能够让同一本体的机器人有足够的泛化能力。
于是作者先做了一个实验：
> We evaluate four visual-input configurations while keeping the target instruction L and robot state fixed. Specifically, we consider: 1) Standard paired input (L, V +), where V + denotes the visual observation originally paired with the instruction in standard VLA training; 2) Visual-removed input (L, ∅), where the visual observation is masked out; 3) Visual-unaligned input (L, V ̃ ), where the visual observation is replaced with an same-scene observation that is not paired with the current instruction; and 4) Visual-conflict input (L, V −), where the visual observation is sampled from a trajectory associated with the opposite motion direction. Since the instruction and robot state remain fixed across all conditions, changes in the predicted action direction can be attributed to the visual input, allowing us to assess whether the policy follows the language instruction or relies on the paired visual observation.

我个人对此的一个很大的疑惑是：当前的pretraining可能完全不能带来泛化能力，因此改变视觉输入是否是一种undefined behavior呢？

但是本文有一个atomic action的设计：**一个短时、连续的轨迹片段，具有单一且占主导的可观察物理效果**。
其定义主要依据四类信息：
- **End-effector motion**：末端执行器的运动
- **Gripper state**：夹爪状态
- **Object status**：是否持有物体
- **Contact mode**：机器人与物体的接触方式
作者按照片段的 **dominant physical effect** 分配唯一的 atomic action，例如：
- `grasp / place`：抓取或释放物体
- `lift / lower / transport`：持物体上移、下移或水平搬运
- `wrist_rotate / reorient`：持物体旋转或调整姿态
- `push / pull / press`：接触式操作
- `move / rotate_free / reorient_free`：空夹爪的自由运动
因此，这里的 “atomic” 并非指运动学上不可再分，而是指在所选语义粒度下，**每个片段只表达一个主要 manipulation intent / physical effect**。