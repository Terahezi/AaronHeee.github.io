---
layout: post
title: "性格对话模型的相关知识"
author: "Aaron He"
tags: 
  - With Caroline
---

# 导语

阅读了 [A Persona-Based Neural Conversation Model](https://arxiv.org/abs/1603.06155) ， 也是第一次接触自然语言处理，这篇文章涉及到基础的Seq2Seq模型、也提出了Persona Embedding的创新点，聚焦解决多轮对话问题。

在此先巩固一下基础知识：



# 什么是seq2seq模型？

### 简介

Seq2Seq 模型是一种**从序列到序列的翻译/对话模型**，由Cho等人于2014年在 [Learning Phrase Representations using RNN Encoder-Decoder for Statistical Machine Translation](https://arxiv.org/abs/1406.1078) 论文中提出。

模型能根据输入的句子，直接**端对端**地生成翻译或是回复，这对传统的需要专家知识或是手动提取特征的翻译/对话模型是一种巨大的超越。

模型结构主要分为三个部分：|Encoder|Hidden State|Decoder|

### Encoder

将输入的句子序列，通过特定网络编码成一个向量，用以表示句子的信息。如果 Encoder 是使用的 RNN（如传统RNN, LSTM 和 GRU）类网络，则最后所取得向量能表示句子的所有信息。

### Hidden State

句子的信息浓缩成Hidden State向量，它成为了 Encoder 和 Decoder 的桥梁，当然也可以针对此处挖掘创新点，比如引入 [Attention Mechanism](https://arxiv.org/abs/1409.0473) , 使得Hidden State更能抓住输入语句的重点信息。

### Decoder

将 Hidden State 解码，使用与Encoder相似的 RNN 类网络，生成相应的句子。

可知整个过程满足了端到端的条件，因此模型被称作 Seq2Seq 。 



> 基础阅读：http://blog.csdn.net/Jerr__y/article/details/53749693
>
> 当前热点：https://zhuanlan.zhihu.com/p/29967933



# 什么是LSTM模型

LSTM是RNN的一种改进模型，全称为**长短期记忆** (Long-Short Term Memory)。

### LSTM传统结构：

便于理解，首先定义一部分符号：

$x_t$  ：第t步的输入信息

$C_t$  :  第t步中Cell包含的信息，包含了前t步的信息，可看做**长期记忆**。

$\hat{C_{t}}$  ：第t步中引入Cell的信息，来自于当前输入，可以看做**短期记忆。**

$h_t$  ：第t步的输出 hidden state，是从当前Cell中提取。

LSTM的更新就是：**用t-1步的长期记忆，和第t步的当下记忆，更新当下长期记忆：**
$$
C_t = f_t \cdot C_{t-1}+i_t \cdot \hat{C_{t}}
$$
其中 $f_t, i_t$ 是**长期记忆**和**短期记忆**在网络中的权重，是 $x_t$ 和 $h_{t-1}$ 拼接后，通过全连接层和Sigmoid函数得出的分值。
$$
f_t = \sigma(W_f\cdot[x, h_{t-1}] + b_{f,t}) \\
i_t = \sigma(W_i\cdot[x, h_{t-1}] + b_{i,t})
$$
$\hat{C_t}$则是经过全连接层后，由tanh函数激活：
$$
\hat{C_t} = tanh(W_c\cdot[x, h_{t-1}] + b_{c,t})
$$
至此只有$h_t$的更新不太明白，与上述相似，$h_t$拥有输出权重$o_t$，更新方式如下：
$$
o_t = \sigma(W_o\cdot[x, h_{t-1}] + b_{o,t})\\
h_t = o_t \cdot tanh(C_t)
$$
若想更可视化地了解LSTM各门的作用，可以参考如下文章：



> http://colah.github.io/posts/2015-08-Understanding-LSTMs/





### GRU结构：





# 如何评价生成对话的好坏？

# 这篇文章的亮点在哪？

# 感想和启发是什么？



