---
alias: spatial-forcing
title: "Spatial Forcing: Implicit Spatial Representation Alignment For Vision-Language-Action Model"
year: "2025"
arxiv_number: "2510.12276"
tags:
  - geometry
opt_url: ""
arxiv_url: https://arxiv.org/abs/2510.12276
code_url: https://github.com/OpenHelix-Team/Spatial-Forcing
need_revisit: false
---
![[spatial-forcing_fig1.png]]
$$\mathcal{L}_{\text{align}} = -\frac{1}{N} \sum_{i=1}^{N} S[\text{MLP} \cdot \Gamma(\boldsymbol{x}_i^{\mathcal{V}}), f_i^{\text{3D}}(I) + E]$$
其中$\Gamma$是batch normalization, $E$是2D sin-cos position encoding, $S[\cdot, \cdot]$是cosine similarity