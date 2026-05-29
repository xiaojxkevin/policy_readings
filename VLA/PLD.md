---
alias: PLD
title: Self-Improving Vision-Language-Action Models with Data Generation via Residual RL
year: "2025"
arxiv_number: "2511.00091"
tags:
  - Data
  - RL
opt_url: https://wenlixiao.com/self-improve-VLA-PLD
arxiv_url: https://arxiv.org/abs/2511.00091
code_url: ""
need_revisit: false
---

### Algorithm
**Require:** $\pi_b, \pi_\delta, Q_\phi, Q_{\phi'}, \alpha, \gamma, \mathcal{B}_{offline}, \mathcal{B}_{online}$
#### Initialization
- Collect $n$ successful trials of $\pi_b$: $\mathcal{D}_{offline} = \{\tau_1, \tau_2, \dots \tau_n\}$
- Initialize online buffer $\mathcal{D}_{online} = \varnothing$
- Initialize the critic network $Q_\phi, Q_{\phi'}$ with Cal-QL on $\mathcal{D}_{offline}$
- Randomly initialize delta policy network $\pi_\delta$
#### RL training
- Freeze $\pi_b$, denote $\bar{\pi}(\cdot|s) = \pi_b(\cdot|s)\pi_\delta(\cdot|s, a_b)$
- **for** each RL step **do**
    - **if** collect data **then**
        - **if** Warm up step **then**
            - base model rollout $a \sim \pi_{base}(\cdot|s)$
        - **else**
            - sample action $\bar{a} \sim \bar{\pi}(\cdot|s)$
        - **end if**
        - Environment step: $r, s', done = env.step(\bar{a})$
        - Add $(s, a, \mu, r, s')$ to buffer $\mathcal{D}_{online}$.
    - **end if**
    - Equally sample data from online and offline buffer: $b \sim \mathcal{D}_{online} \cup \mathcal{D}_{offline}$
    - Calculate TD target by bootstrapping $\bar{\pi}$
    - Update $Q_\phi$ by Equation (2)
    - Update $\pi_\delta$ by maximizing the SAC target
    - **Polyak update** $\phi' = \rho\phi' + (1 - \rho)\phi$
- **end for**
#### Base policy SFT
For each task, we collect hybrid behavior dataset $\mathcal{D}_{SFT}$:
$$\pi(s_t) = \begin{cases} a_{base}, & t < T_{base} \\ a_{base} + a_\delta, & t \ge T_{base} \end{cases}$$
- **for** each SFT step **do**
    - update $\pi_b$ by BC objective.
- **end for**
- **Return** $\pi_b$
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
