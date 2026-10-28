---
alias: X-Tokenizer
title: "X-Tokenizer: A Multimodal Action Tokenizer for Vision-Language-Action Pretraining"
year: "2026"
arxiv_number: "2606.14752"
tags:
  - tokenize
opt_url: ""
arxiv_url: https://arxiv.org/abs/2606.14752
code_url: https://github.com/X-Square-Robot/X-Tokenizer
need_revisit: false
---
我感觉这篇工作的出发点是FAST tokens的缺点：
$\underbrace{A}_{[B, T, D]} \xrightarrow{\;\text{DCT}\;} \underbrace{C}_{[B, T, D]} \xrightarrow{\;\text{Quantize}\;} \underbrace{\bar{C}}_{[B, T, D]} \xrightarrow{\;\text{BPE}\;} \underbrace{\text{Action IDs}}_{[B, L]}$
Action ID与真实action “类型”等的对应关系是不非常弱的，理论上只是通过某种可逆方式定义好了action encode - decode的过程。因此一个非常值得延伸的问题是：是否能让action处于VLM backbone中的某种近似的语义空间中？从直觉上看这样应该能让tokenizer更好的。
x-tokenzier就是这么操作的：
![[x-tokenizer_fig1.png]]
它的预训练主要考虑的因素是：1）与frozen VLM的feature对齐；2）自动的encode and decode；3）对discrete tokens的mask reconstruction，意在学习chunk内部的结构。
在pretrain之后，可以非常自由的接入后续的VLA模型：提供token mapping自然有ID的，但是这样的tokenizer具备更好的结构。

但显然问题也有很多：作者用了很多笔墨想说明这个tokenizer正确align features了，但是和spatial forcing一样，这样的alignment对action是否真的做对动作，一定是正相关的吗？目前论文并没有一个很好的ablation说明去除掉alignment等，在success rate上到底如何。
