---
alias: Action-Sketcher
title: "Action-Sketcher: From Reasoning to Action via Visual Sketches for  Long-Horizon Robotic Manipulation"
year: "2026"
arxiv_number: "2601.01618"
tags:
  - Aug
  - subtask
opt_url: ""
arxiv_url: https://arxiv.org/abs/2601.01618
code_url: https://github.com/FlagOpen/Action-Sketcher
need_revisit: false
---
## 1. Sketch 设计

Visual Sketch 由三类 2D primitive 组成：

- **BBox**：定位目标物体或操作区域，回答“操作哪个对象”。
- **Point**：表示抓取点、部件位置、目标位置或几何参考点，回答“具体在哪里操作”。
- **Arrow**：表示运动方向或旋转意图，回答“怎么移动/旋转”。

其中，translation arrow 由起点和终点定义，用于描述平移；rotation arrow 由中心点、旋转轴和方向定义，用于描述旋转。

整体可概括为：

> **BBox = what，Point = where，Arrow = how。**

Sketch 是 **sub-task 级别** 的：每完成一个子任务后重新 reasoning，并生成新的 sketch，而不是一次性预测整段长时序轨迹。

## 2. 三阶段训练

### Stage 1：Spatiotemporal Pretraining

目标是先学习空间理解和时间规划能力，不训练 action head。

数据主要包括：

- Visual grounding：学习 bbox 定位；
- Object pointing / affordance：学习 point 和可操作部位；
- Spatial perception：学习空间关系；
- Temporal planning：学习任务分解和子任务顺序。

输入通常是 **图像 + 文本**，输出是 grounding、pointing、spatial reasoning 或 planning 结果。

### Stage 2：Reasoning-to-Sketch

目标是学习：

> **场景 + 任务 + 历史 → 下一子任务 + reasoning + sketch**

数据来自：

- 约 2.6k 条真实机器人 long-horizon demonstrations；
- 约 1.7k 条 LIBERO / RoboTwin 轨迹；
- 最终构造约 21k 个 reasoning-to-sketch samples。

Sketch 标注先通过自动流程生成，再由人工修正。自动流程利用 gripper 状态切分 sub-task，用 SAM 和物体位移确定目标物体，再由 GPT-4o生成 sub-task、reasoning 和 `{bbox, points, arrow}`。

输出主要包括：

1. temporal reasoning；
2. 当前 sub-task；
3. spatial reasoning；
4. JSON 格式的 sketch。

### Stage 3：Sketch-to-Action

目标是学习：

> **多视角图像 + sketch + robot state → action chunk**

数据包括 LIBERO、RoboTwin 2.0 和真实机器人示范，共约 6.1k episodes、2.8M action samples。

同一 sub-task 内的所有 action frames 共享同一个 sketch。模型同时学习两种模式：

- `<BOR>`：需要重新 reasoning；
- `<BOA>`：直接执行 action。

为避免模型总是选择 action mode，训练时对 reasoning 和 action samples 采用约 **50% / 50%** 的采样比例。

此外，Stage 3 会对 bbox、point 和 arrow 加扰动，以提高控制器对不完美 sketch 的鲁棒性。