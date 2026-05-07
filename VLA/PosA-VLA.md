---
alias: PosA-VLA
title: "PosA-VLA: Enhancing Action Generation via Pose-Conditioned Anchor Attention"
year: "2025"
arxiv_number: "2512.03724"
tags:
  - manipulation
opt_url: ""
arxiv_url: https://arxiv.org/abs/2512.03724
code_url: ""
need_revisit: false
---
作者遇到的问题是：
![[posA-VLA.png|697]]
那么一个解决方案就是通过supervised learning监督网络使其能够attend到正确的区域。作者因此提出了**Task-relevant anchor** 和 **End-effector anchor**两个anchor。
- 在进行hand-eye calibration后，作者能够将EE分别投影到wrist和head相机中（坐标），然后使用二维高斯分布图计算GT。
- Task-relevant Anchor并非在每个时间步都存在。它仅在末端执行器状态发生改变（例如，夹爪闭合或张开）的时间步被提取和定义 。这种状态改变被视为机器人正在与任务相关的目标区域发生物理交互的数学标志 。
- End-effector Anchor在**每一个时间步 $t$（Every timestep）** 都会被计算和定义。
###### More on Task-relevant Anchor
一个很简单的问题：task-relevant是通过夹爪开合程度来判断的，那么在机械臂“去往路途中”，怎么给监督信号？
Gemini:
**Task-relevant anchor 在“路程上”是持续存在并提供监督信号的，它的本质是将“未来的交互点”在“当前的时间步”进行了动态投影。**
具体机制如下：
1. **确定“目标锚点”的绝对物理坐标**
在处理一段演示轨迹（Demonstration）时，算法会先遍历这整条轨迹，定位到夹爪状态发生改变（比如闭合抓取）的那一帧 $t_{interact}$。
它会提取这一帧夹爪的 3D 空间坐标，我们将其记为 **$P_{target}$**。因为物体通常是静止的，这个 $P_{target}$ 在物理空间中就代表了“任务相关的绝对区域”（比如面包的位置）。
2. **在“路程上”进行逐帧反投影**
在机械臂向目标移动的**每一个时间步 $t$**（即你所说的“路程上”）：
- **任务相关锚点 ($F_f^{task}$)**：算法会将那个**固定的 3D 目标坐标 $P_{target}$**，利用时间步 $t$ 时的相机变换矩阵，投影到当前的 2D 图像平面上。以这个投影点为中心生成较大的高斯图。
- **末端执行器锚点 ($F_f^{end}$)**：算法会将**当前的 3D 夹爪坐标 $p_t$**，投影到当前的 2D 图像平面上。以这个投影点为中心生成较小的高斯图。    
这就解释了为什么在论文的附录可视化图（如 Figure 7 或 Figure 15）中，尽管机械臂还在大老远的地方移动，代表 Task-relevant 的**绿色圆圈（注意力）却始终死死地套在目标物体上**——因为那是 3D 目标点在当前相机视角下的投影。
**3. 巧妙的“容错机制”（信号缺失处理）**
你在思考这个问题时可能已经察觉到了一个盲区：如果在移动初期，目标物体根本不在相机的视野（FOV）内怎么办？（尤其是跟随机械臂移动的腕部相机，一开始大概率拍不到目标）。
这就是论文 Section 3.3 中提到 **Batch-wise Contrastive Loss** 时那个特殊判断逻辑的来源：
> _"A signal is considered available only if the length of $\Omega^+$ is greater than zero; otherwise, the corresponding embedding is replaced with zero-padding..."_
如果在时间步 $t$，“固定的 3D 目标坐标 $P_{target}$” 投影到了 2D 图像的边界之外（即目标不在视野内），那么生成的 $F_f^{task}$ 就是一张**全 0 的空白图**。

此时，提取出的高激活正样本索引集 $\Omega^+$ 的长度为 0。算法就会判定当前帧的 Task-relevant 信号**不可用（Unavailable）**，并使用 Zero-padding 将对应的特征屏蔽掉。

**总结来说：**
在路程上，模型通过“上帝视角”预先拿到了这趟行程的终点 3D 坐标，并在每一帧把它投影成 2D 高斯图来教导模型：“不管你现在在哪，你的眼睛得一直盯着这个投影位置”。如果视野里确实看不到，就不给监督（Zero-padding），防止模型学乱。
###### 问题：
这个方法其实已经限制死pick-and-place的任务了，像叠衣服这类non-rigid的task，这样的anchor map其实并不容易获取。