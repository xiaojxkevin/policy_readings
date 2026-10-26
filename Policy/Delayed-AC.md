---
alias: Delayed-AC
title: |-
  Why Does Action Chunking Improve Behavioral
  Cloning Performance in Robotic Control?
year: "2026"
arxiv_number: "2608.02547"
tags:
  - Inference
opt_url: ""
arxiv_url: https://arxiv.org/pdf/2608.02547
code_url: https://action-chunking.github.io/
need_revisit: false
---
**核心结论：Action Chunking 的优势主要来自学习和利用多种时间跨度的动作预测关系，而不一定来自连续执行多个动作。**

1. **Non-Markovian Expressivity**
   - 人类演示可能具有非马尔可夫性。
   - 使用历史 observation 预测当前 action，有时比使用当前 observation 更准确。

2. **Compounding Error Reduction**
   - 使用历史 observation 可以减缓闭环控制中的误差累积。
   - 不一定需要通过 Action Chunking 缩短 effective decision horizon。

3. **Representation Learning**
   - 训练时预测完整 action chunk，相比单步 BC 有明显收益。
   - 但直接训练预测未来单步动作的 Delayed Policy，也能获得大部分收益。

4. **Implicit Ensembling**
   - Action Chunking 同时学习多个不同时间跨度的预测关系：

     $$
     p(a_t \mid o_t),\;
     p(a_t \mid o_{t-1}),\;
     \ldots,\;
     p(a_t \mid o_{t-H+1})
     $$

   - 这些预测关系共享模型参数，但具有不同的 conditioning observation 和 prediction horizon。
   - 组合不同 delay 的预测，可以获得类似 ensemble 的性能优势。

**Key Insight：Action Chunking 不仅是一种生成连续动作序列的方法，也可以被理解为一个共享参数的、多 prediction horizons 的策略集合。**

---

## 2. AC(10)-RDE Implementation

### 2.1 Training

与标准 Action Chunking 完全相同，使用 Behavioral Cloning 训练 Diffusion Policy：

$$
\pi_\theta(A_{t:t+19}\mid o_t)
$$

其中：

- Action chunk length: 20
- Action prediction horizon: 20
- Training target: 未来 20 个动作
- Training objective: Diffusion noise prediction loss
- 无额外 RDE training 或 fine-tuning

训练时，模型学习从同一个 observation 预测未来 20 个动作，因此隐式学习多个 prediction horizons。

### 2.2 Inference

每个控制步随机选择一个 delay，从对应的历史 observation 生成 action chunk，然后只执行与当前时刻对应的动作。

$$
d_t \sim \mathcal U\{0,\ldots,9\}
$$

$$
a_t = [\pi_\theta(o_{t-d_t})]_{d_t}
$$

这里 $[\cdot]_{d_t}$ 表示取 action chunk 中索引为 $d_t$ 的动作（zero-based indexing）。

```python
# AC(10)-RDE: Simulation

for t in control_loop:
    # Sample a random delay
    d = random.randint(0, 9)

    # Select historical observation
    obs = obs_buffer[t - d]

    # Generate a full action chunk
    chunk = diffusion_policy.sample(obs)
    # shape: [20, action_dim]

    # Select the action corresponding to current time
    action = chunk[d]

    # Execute only one action
    robot.execute(action)
```

因此，RDE 每个控制步都重新选择一个 delay，并执行一个动作，不进行 temporal averaging。

### 2.3 Real-Robot Deployment

真机采用 asynchronous inference，因此引入至少一个控制步的 observation delay：

$$
d_t \sim \mathcal U\{1,\ldots,10\}
$$

$$
a_t = [\pi_\theta(o_{t-d_t})]_{d_t}
$$

主要配置：

- Control frequency: 15 Hz
- Action chunk length: 20
- Diffusion denoising steps: 15
- Denoising network: 5-layer MLP, 2000 hidden units
- Asynchronous inference：推理与机器人动作执行并行
- 每个控制步生成一个 chunk，但只执行一个 action
- 不进行 temporal averaging，不需要多个独立模型

真机使用 delta joint position control。虽然 policy 基于历史 proprioception 预测关节增量，但底层执行时会使用当前关节位置：

$$
q_t^{\mathrm{target}}
=
q_t^{\mathrm{current}}+\Delta q_t
$$
