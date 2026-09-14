---
alias: CoFreeVLA
title: "COFREEVLA: COLLISION-FREE DUAL-ARM MANIPULATION VIA  VISION-LANGUAGE-ACTION MODEL AND RISK ESTIMATION"
year: "2026"
arxiv_number: "2601.21712"
tags:
  - Collision
opt_url: ""
arxiv_url: https://arxiv.org/pdf/2601.21712
code_url: ""
need_revisit: false
---
### 1. Training: Data Collection & Supervision
#### 1.1 Pre-train（离线，合成标签）
**数据生成：** 在 simulation 中 rollout VLA policy。对每个 state $s_t$，VLA 生成 action sequence $A_{t:t+H-1}$（$H=5\sim10$）。为增加 coverage，施加 **jitter**（随机噪声扰动）和 **beam variants**（beam search 生成多候选），形成多样化的 $(s_t, A_{t:t+H-1})$ 对。
**标签生成（Model-based Checker）：** 使用已知的 robot URDF link meshes + **FCL (Flexible Collision Library)** 做 mesh 间距离查询，沿 H 步 rollout 判定：
- $y_t^{\text{bin}} \in \{0, 1\}$：未来 H 步内是否碰撞
- $y_t^d$（mm）：最小 inter-arm / arm-grasped-object 距离
- $y_t^{\text{ttc}}$（s）：首次碰撞时间
策略上过采样小 $y_t^d$ 样本（near-miss emphasis），并采用 curriculum on horizon H（从短序列逐渐拉长）。
**损失函数：**
$$
L = \lambda_{\text{bce}} \operatorname{BCE}(\hat{r}_t, y_t^{\text{bin}}) + \lambda_d \|\hat{d}_{\min} - y_t^d\|_2^2 + \lambda_{\text{ttc}} \|\hat{\tau}_{\text{ttc}} - y_t^{\text{ttc}}\|_1
$$
对 false negative 增大权重。训练后做 temperature scaling 校准 $\hat{r}_t \in [0,1]$。
#### 1.2 Post-train（在线，真实标签）
在真实机器人上闭环部署，用物理信号重新标注：
- **Contact events**：力/力矩传感器检测的碰撞
- **Torque spikes**：关节力矩异常跳变
- **Vision overlap**：相机几何重叠
采用 **DAgger-like** 迭代：部署（risk gate 开启）→ 收集 $(s_t, A_{t:t+H-1}, \hat{r}_t, \text{contact}, \text{corrected action})$ → 同时更新 $E_\phi$ 和 VLA → 重新部署。
**两个阶段的本质区别：** 标签来源不同（mesh-distance vs. 物理传感器），但训练算法相同。Pre-train 提供 warm-start，post-train 校准 sim-to-real gap。
#### 1.3 风险估计器架构 $E_\phi$
双分支输入：
- **Proprio/Action stream**：$(x_t, g_t)$ + $A_{t:t+H-1}$ → 长度 H 的 token 序列 + positional encoding
- **Vision stream**：VLA backbone 的 penultimate visual token $z_t = f_{\text{VLA}}(o_t)$
Cross-attention 融合 → pooling → 三个预测头：$\hat{r}_t$, $\hat{d}_{\min}$, $\hat{\tau}_{\text{ttc}}$。推理耗时 < 5ms。
### 2. Inference & Base Model
#### 2.1 Run-time 控制流程
```
VLA proposal → Eϕ 评估 risk r̂_t

├─ r̂_t ≤ τ↓ → 执行（soft gate: α = clip(1 - r̂/τ↑, 0, 1) 缩放速度）

└─ r̂_t > τ↑ → Block → Recovery:

min_A Eϕ(s_t, A) + λ||A||²

(projected gradient descent, joint/velocity limits 下)

→ 连续 K cycle 内 r̂ ≤ τ↓ → 恢复 VLA

```

Recovery 的核心：$E_\phi$ 既是 predictor 也是 objective——对 action 求导，沿 $\nabla_A E_\phi$ 方向搜索安全替代动作。Hysteresis（$\tau_\downarrow < \tau_\uparrow$）防止 chattering。
#### 2.2 Base Model & 设置
- **VLA**：RDT-1B（diffusion foundation model for bimanual manipulation），@10Hz
- **Low-level controller**：@30Hz
- **平台**：AgileX-based 双臂（PiPER arms），5 个双臂任务（Pouring Beans, Pen Cap Removal, Tool Handover, Tubes Placement, Cups Nesting）
- **对比 baseline**：RDT-1B, APEX
### 3. 一点讨论
#### 3.1 Risk Estimator 与 VLA 行为分布的强绑定
$E_\phi$ 的训练数据来自 **特定 VLA 在该机器人平台上的 rollout + jitter**。这带来两个后果：
1. $E_\phi$ 学到的是"在**这个 VLA** 输出附近，多远会碰到边界"，而非通用的 collision predictor。
2. 换 VLA 或换机器人平台 → 必须重新采集数据。
更本质的问题是：如果 VLA 本身行为集中在安全区域（如大量双臂遥操作数据精调后的版本），$E_\phi$ 的训练数据中碰撞样本极少，其判别能力存疑。**换言之，$E_\phi$ 之所以有效，部分原因恰恰是 RDT 在双臂场景下频繁输出危险动作（碰撞率 8/10）**。我们完全可以换成pi0.5这种非常强的foundation model，很可能这个需求是不存在的。
#### 3.2 Recovery 走入了 Eϕ 的 OOD 区域
Recovery 用梯度下降解 $\min_A E_\phi(s_t, A) + \lambda\|A\|^2$。问题是：**梯度优化可能走到 $E_\phi$ 训练分布之外的动作空间区域。** $E_\phi$ 的 training distribution 是"VLA 输出 + jitter 半径"，而梯度下降不受此约束——$E_\phi$ 在 OOD 区域的预测可能极不可靠（neural network 对 OOD 样本容易给出过度自信的判断）。论文对此没有分析。
#### 3.5 双臂设计的浅层性
论文对双臂的特殊处理止步于 input/output 维度（$q_t = (q_t^L, q_t^R)$，检测 inter-arm + arm-object 碰撞），**没有在网络架构或 safety mechanism 中融入双臂的结构化 inductive bias**（如双 kinematic chain 的关系建模、臂间优先级协调等）。方法移植到单臂做环境碰撞 avoidance 需要的改动很小。