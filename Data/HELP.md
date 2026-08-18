---
alias: HELP
title: |-
  HELP: HUMAN-EFFICIENT LARGE-SCALE ROBOT
  POST-TRAINING WITH ROLLOUT SEGMENTATION
year: "2026"
arxiv_number: "2607.09776"
tags:
  - human-in-the-loop
opt_url: ""
arxiv_url: https://arxiv.org/pdf/2607.09776
code_url: https://github.com/InternRobotics/VLAC-cut
need_revisit: false
---


> [!summary]
> HELP 的核心目标不是“让人类采更多 demonstration”，而是让**少量人类同时驱动大量机器人持续 rollout，并把其中最有价值的片段自动变成下一轮训练数据**。

## Pipeline

```text
                ┌──────────────────────────────┐
                │   当前 VLA Policy 部署到真实机器人   │
                └──────────────┬───────────────┘
                               │
                               ▼
                  12 台机器人并行 Autonomous Rollout
                               │
                 ┌─────────────┴─────────────┐
                 │                           │
                 ▼                           ▼
        正常 autonomous execution      出现 failure-prone state
                 │                           │
                 │                           ▼
                 │                 Floor Operator 监控 robot fleet
                 │                 - 同时观察多台机器人
                 │                 - 识别重复 failure mode
                 │                 - 触发 takeover
                 │                 - 负责环境 reset
                 │                           │
                 │                           ▼
                 │                 Teleoperator 远程接管
                 │                 - 只做高技能纠错
                 │                 - 提供 recovery demonstration
                 │                           │
                 │                           ▼
                 │                       HITL Data
                 │
                 ▼
          Autonomous Rollout
                 │
                 ▼
              VLAC-CUT
      对 rollout 做 process-level segmentation
                 │
      ┌──────────┼───────────┬───────────┐
      ▼          ▼           ▼           ▼
   Progress    Recovery      Idle     Failure-inducing
      │          │           │           │
      └────保留──┘           └────丢弃───┘
                 │
                 ▼
          Curated Rollout Data
                 │
                 ▼
   Base Demo + HITL Recovery + Curated Rollout
                 │
                 ▼
              Post-Training
                 │
                 ▼
            更强的 VLA Policy
                 │
                 └──────────────► 下一轮部署
```

#### 它如何 scale up？

核心是把传统的：

$$
1\ \text{human} \approx 1\ \text{robot}
$$

变成：

$$
\boxed{2\ \text{operators} \rightarrow 12\ \text{robots}}
$$

其中两个人做**角色专业化**：

- **Floor Operator**：负责同时监控机器人集群、发现 failure、触发 takeover、做 reset。
- **Teleoperator**：不负责监控和 reset，只在关键时刻远程接管，产生高质量 recovery demonstration。

这样，高技能 teleoperation 时间只被用在**真正有信息量的失败状态**上，而不是浪费在等待、巡检和重置环境。

#### 它如何提高数据采集效率？

HELP 并不把所有 autonomous rollout 都直接训练，而是让 VLAC-CUT 自动判断：

$$
\text{哪些片段值得学习？}
$$

只保留：

$$
\boxed{\text{Progress-making} + \text{Recovery}}
$$

丢弃：

$$
\boxed{\text{Idle} + \text{Failure-inducing}}
$$

因此每轮训练使用：

$$
D_{\text{train}}
=
D_{\text{base}}
+
D_{\text{HITL}}
+
D_{\text{curated autonomous}}
$$

关键在于：**少量 HITL 数据负责指出模型不会什么，大量机器人自主 rollout 负责扩展数据规模，VLAC-CUT 负责把自主数据变成可用训练数据。**

最终形成数据飞轮：

$$
\boxed{
\text{More robots}
\rightarrow
\text{more failures exposed}
\rightarrow
\text{targeted human correction}
\rightarrow
\text{more useful rollout}
\rightarrow
\text{better policy}
\rightarrow
\text{better future rollout}
}
$$

