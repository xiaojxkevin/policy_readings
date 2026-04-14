---
alias: RTC
title: Real-Time Execution of Action Chunking Flow Policies
year: "2025"
arxiv_number: "2506.07339"
tags:
  - Inference
opt_url: https://openreview.net/forum?id=UkR2zO5uww
arxiv_url: https://arxiv.org/abs/2506.07339
code_url: https://github.com/Physical-Intelligence/real-time-chunking-kinetix
need_revisit: true
---
# Inference-time RTC
![[rtc_inference.png]]
$$
\begin{aligned} \mathbf{v}_{\Pi\text{GDM}}(\mathbf{A}_t^\tau, \mathbf{o}_t, \tau) &= \mathbf{v}(\mathbf{A}_t^\tau, \mathbf{o}_t, \tau) + \min\left( \beta, \frac{1 - \tau}{\tau \cdot r_\tau^2} \right) \left( \mathbf{Y} - \widehat{\mathbf{A}}_t^1 \right)^\top \operatorname{diag}(\mathbf{W}) \frac{\partial \widehat{\mathbf{A}}_t^1}{\partial \mathbf{A}_t^\tau} \\ \text{where } \widehat{\mathbf{A}}_t^1 &= \mathbf{A}_t^\tau + (1 - \tau)\mathbf{v}(\mathbf{A}_t^\tau, \mathbf{o}_t, \tau), \\ r_\tau^2 &= \frac{(1 - \tau)^2}{\tau^2 + (1 - \tau)^2}. \end{aligned}
$$

# Training-time RTC
https://arxiv.org/pdf/2512.05964

![[rtc_training.png]]add the previous chunk in delay d as priors to the action expert.