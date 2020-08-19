### seq2seq
seq2seq 是一个 Encoder–Decoder 结构的网络，它的输入是一个序列，输出也是一个序列。  
Encoder 中将一个可变长度的信号序列变为固定长度的向量表达，Decoder 将这个固定长度的向量变成可变长度的目标的信号序列。   
这个结构最重要的地方在于输入序列和输出序列的长度是可变的，可以用于翻译，聊天机器人，句法分析，文本摘要等。 

Cho 在 2014 年提出了 Encoder–Decoder 结构，即由两个 RNN 组成（其中的 RNNCell 可以用 RNN ，GRU，LSTM 等结构）。    
Sutskever 在 2014 年也发表了论文：这个模型结构更简单， Encoder 和 Decoder 都用的 LSTM 结构，注意每句话的末尾要有 “EOS” 标志。 
### Attention
Attention 整体架构看似复杂，其实就是一个Seq2Seq结构，   
Encoder的每一层有两个操作，分别是Self-Attention和Feed Forward；  
而Decoder的每一层有三个操作，分别是Self-Attention、Encoder-Decoder Attention以及Feed Forward操作。   
这里的Self-Attention和Encoder-Decoder Attention都是用的是Multi-Head Attention机制。

Attention函数的本质可以被描述为一个查询（query）到一系列（键key-值value）对的映射。  
计算attention时主要分为三步:
```
第一步是将query和每个key进行相似度计算得到权重，常用的相似度函数有点积，拼接，感知机等；

然后第二步一般是使用一个softmax函数对这些权重进行归一化；

最后将权重和相应的键值value进行加权求和得到最后的attention。
```
目前在NLP研究中，key和value常常都是同一个，即key=value。

放缩点积attention（scaled dot-Product attention），使用点积进行相似度计算的attention，只是多除了一个（为k的维度）起到调节作用，使得内积不至于太大。

Self-attention即K=V=Q，例如输入一个句子，那么里面的每个词都要和该句子中的所有词进行attention计算。目的是学习句子内部的词依赖关系，捕获句子的内部结构。
