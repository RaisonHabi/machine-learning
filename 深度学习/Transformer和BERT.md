## BERT
完全放弃了以往经常采用的RNN和CNN，提出了一种新的网络结构，即Transformer，其中包括encoder和decoder，我们只关注encoder。

图2.1是Transformer encoder的结构，后文中我们都简称为Transformer。首先是输入word embedding，这里是直接输入一整句话的所有embedding。如图2.1所示，假设我们的输入是Thinking Machines，每个词对应一个embedding，就有2个embedding。输入embedding需要加上位置编码（Positional Encoding），为什么要加位置编码，后文会做详细介绍。然后经过一个Multi-Head Attention结构，这个结构是算法单元中最重要的部分，我们会在后边详细介绍。之后是做了一个shortcut的处理，就是把输入和输出按照对应位置加起来，如果了解残差网络（ResNet）的同学，会对这个结构比较熟悉，这个操作有利于加速训练。然后经过一个归一化normalization的操作。接着经过一个两层的全连接网络，最后同样是shortcut和normalization的操作。可以看到，除了Multi-Head Attention，都是常规操作，没有什么难理解的。这里需要注意的是，每个小模块的输入和输出向量，维度都是相等的，比如，Multi-Head Attention的输入和输出向量维度是相等的，否则无法进行shortcut的操作；Feed Forward的输入和输出向量维度也是相等的；最终的输出和输入向量维度也是相等的。但是Multi-Head Attention和Feed Forward内部，向量维度会发生变化。

&nbsp;
## reference
[自然语言处理中的Transformer和BERT](https://zhuanlan.zhihu.com/p/53099098)
