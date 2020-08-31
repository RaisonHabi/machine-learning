## 一、seq2seq
**seq2seq 是一个 Encoder–Decoder 结构的网络**，它的输入是一个序列，输出也是一个序列。  
Encoder 中将一个可变长度的信号序列变为固定长度的向量表达，Decoder 将这个固定长度的向量变成可变长度的目标的信号序列。   
这个结构最重要的地方在于输入序列和输出序列的长度是可变的，可以用于翻译，聊天机器人，句法分析，文本摘要等。 

Cho 在 2014 年提出了 Encoder–Decoder 结构，即由两个 RNN 组成（其中的 RNNCell 可以用 RNN ，GRU，LSTM 等结构）。    
Sutskever 在 2014 年也发表了论文：这个模型结构更简单， Encoder 和 Decoder 都用的 LSTM 结构，注意每句话的末尾要有 “EOS” 标志。 

&nbsp;
## 二、Attention
**Attention 整体架构看似复杂，其实就是一个Seq2Seq结构**，   
**Encoder的每一层有两个操作，分别是Self-Attention和Feed Forward**；  
**而Decoder的每一层有三个操作，分别是Self-Attention、Encoder-Decoder Attention以及Feed Forward**操作。   
这里的Self-Attention和Encoder-Decoder Attention都是用的是Multi-Head Attention机制。

Attention函数的本质可以被描述为一个查询（query）到一系列（键key-值value）对的映射。  
计算attention时主要分为三步:
```
第一步是将query和每个key进行相似度计算得到权重，常用的相似度函数有点积，拼接，感知机等；

然后第二步一般是使用一个softmax函数对这些权重进行归一化；

最后将权重和相应的键值value进行加权求和得到最后的attention。
```
**目前在NLP研究中，key和value常常都是同一个，即key=value**。

放缩点积attention（scaled dot-Product attention），使用点积进行相似度计算的attention，只是多除了一个（为k的维度）起到调节作用，使得内积不至于太大。

**Self-attention即K=V=Q，例如输入一个句子，那么里面的每个词都要和该句子中的所有词进行attention计算。目的是学习句子内部的词依赖关系，捕获句子的内部结构**。

**attention机制可以灵活的捕捉全局和局部的联系，而且是一步到位的**。  
**从attention函数可以看出，它 先是进行序列的每一个元素与其他元素的对比，在这个过程中每一个元素间的距离都是一，因此它比时间 序列RNNs的一步步递推得到长期依赖关系好的多，越长的序列RNNs捕捉长期依赖关系就越弱**。

**并行计算减少模型训练时间 ：Attention机制每一步计算不依赖于上一步的计算结果，因此可以和CNN一样并行处理**

&nbsp;
### 1.Attention详解
[Attention机制详解（一）——Seq2Seq中的Attention](https://zhuanlan.zhihu.com/p/47063917)   
[Attention机制详解（二）——Self-Attention与Transformer](https://zhuanlan.zhihu.com/p/47282410)  
[The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)

先来看一个翻译的例子“I arrived at the bank after crossing the river” 这里面的bank指的是 银行还是河岸呢，这就需要我们联系上下文，当我们看到river之后就应该知道这里bank很大概率指的是 河岸。  
在RNN中我们就需要一步步的顺序处理从bank到river的所有词语，而当它们相距较远时RNN的效果 常常较差，且由于其顺序性处理效率也较低。  
Self-Attention则利用了Attention机制，计算每个单词与 其他所有单词之间的关联，在这句话里，当翻译bank一词时，river一词就有较高的Attention score。 利用这些Attention score就可以得到一个加权的表示，然后再放到一个前馈神经网络中得到新的表示， 这一表示很好的考虑到上下文的信息。


对于self-attention来讲，Q(Query), K(Key), V(Value)三个矩阵均来自同一输入，首先我们要计算Q 与K之间的点乘，然后为了防止其结果过大，会除以一个尺度标度，再利用Softmax操作将其结果归一化为 概率分布，然后再乘以矩阵V就得到权重求和的表示。


#### position encoding:
由于该模型没有recurrence或convolution操作，所以没有明确的关于单词在源句子中位置的相对或绝对 的信息，**为了更好的让模型学习位置信息，添加了position encoding并将其叠加在word embedding上**。 该论文中选取了三角函数的encoding方式，其他方式也可以.


#### Add & Norm模块:
**Add代表了Residual Connection(残差连接)，是为了解决多层神经网络训练困难的问题，通过将前一层 的信息无差的传递到下一层，可以有效的仅关注差异部分**.
**而Norm则代表了Layer Normalization，通过对层的激活值的归一化，可以加速模型的训练过程，使其更 快的收敛**.


完全的不依赖于RNN结构仅利用Attention机制的Transformer由于其并行性和对全局信息的有效处理使其 获得了较之前方法更好的翻译结果，在这基础上，Attention和Transformer架构逐步被应用在自然语言 处理及图像处理等领域。
