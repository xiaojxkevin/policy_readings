---
alias: CLASS
title: |-
  CLASS: Contrastive Learning via Action Sequence
  Supervision for Robot Manipulation
year: "2025"
arxiv_number: "2508.01600"
tags:
  - contrastive-learn
opt_url: ""
arxiv_url: https://arxiv.org/pdf/2508.01600
code_url: ""
need_revisit: false
---
首先文章需要预先统计pair-wise action chunk的相似度，这一步本身就是time-costing的
然后就是分别训练latent feauturs和policy head
![[class_overview.png]]
==这里我没有特别清楚的细节是：每个训练的batch中应该包含哪些数据？==
