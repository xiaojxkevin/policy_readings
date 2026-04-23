---
alias: OpenVLA
title: "OpenVLA: An Open-Source Vision-Language-Action Model"
year: 2024
arxiv_number: "2406.09246"
tags:
  - manipulation
opt_url:
arxiv_url: https://arxiv.org/abs/2406.09246
code_url: https://github.com/openvla/openvla
need_revisit: false
---

![[openvla.png]]
本质上来说，OpenVLA其实没有多余的action expert，基本上完全依靠其VLM了。同时它也没有像pi0那种针对robot data的pre-training和post-training
###### De-Tokenizer:
这篇文章没有使用后来流行的扩散模型直接生成action，而是输出**离散**的action token。那么这其实也意味着几件事：
1. 需要预先对output space进行统计并分成256 bin: 
	> For each action dimension, we set the bin width to uniformly divide the interval between the 1st and 99th quantile of the actions in the training data
2. Llama2 的vocabulary大小为32000，使用subword tokenization可以handle大部分文本内容了，但是只预留了100个empty tokens (e.g., `<reserved_1>` ...)，这些token在原先model中没有具体意义。那么对于这里的256 bin，100个是不够的。作者的做法很有趣，就是用31744-31999，也就是改变最不常用的256个token当成action的语义来使用。
3. Llama是可以输出不定长结果的，如何保证它一定输出7个output token呢？
	1. 在训练过程中，强制7个输出并一一对应，以`<EOS>` token结尾；
	2. 在inference阶段设置其自回归`max_new_tokens = 7` 
###### Inference
这篇文章是single-step prediction，也就是每次只预测下一步的内容。作者用了quantization进行加速，最终在4090上能有一个6Hz的推理速度。
###### Takeaway
> However, we found fine-tuning the vision encoder during VLA training to be crucial for good VLA performance

说明对于VLA而言，Vision信号是一个非常重要的因素
