## 一、DeepWalk: Online Learning of Social Representations
DeepWalk可以称得上是这个方向上最著名的工作了，几乎所有网络表示学习相关的论文，都会引用DeepWalk作为Baseline之一，同时DeepWalk也是很多相关工作所使用的底层方法之一。  

**DeepWalk最主要的贡献就是他将Network Embedding与自然语言处理中重要的Word Embedding方法Word2Vec联系了起来，使得Network Embedding问题转化为了一个Word Embedding问题**。

**转化方法其实很简单，就是随机游走**。如下图所示，DeepWalk通过从每个结点出发n_walks次，每一步都采取均匀采样的方式选择当前结点的邻接结点作为下一步的结点随机游走。当游走的路径长度达到walk_length后，停止一次游走。这样就生成了一个个游走的序列，每个序列都称为一个walk。每个walk都被当成Word2Vec中的一个句子，而每个结点都是Word2Vec中的一个词。

**之后的算法几乎和Word2Vec的Skipgram版本完全一样**。使用一个大小为window_size的滑动窗口作为一条walk的context，使用一个context中的中心词去推测所有context中的其他词，使用的目标函数也与Word2Vec一致。

&nbsp;
## 二、LINE: Large-scale Information Network Embedding
虽然DeepWalk通过随机游走的方式，将图结构数据转化为了自然语言处理的任务来完成。   
**但是，图结构中节点的关系往往比词上下文关系更加的复杂。通常部分的图结构数据中边具有权重，使用现有的Word2Vec方法无法很好的应对这个问题**。   
此外，在现实世界数据中，图的规模往往过于庞大，以至于存下所有的walk的开销将十分惊人。

**LINE不再采用随机游走的方法。相反，他在图上定义了两种相似度——一阶相似度与二阶相似度**。
```
一阶相似度：一阶相似度就是要保证低维的嵌入中要保留两个结点之间的直接联系的紧密程度，
换句话说就是保留结点之间的边权，若两个结点之间不存在边，那么他们之间的一阶相似度为0。
例如下图中的6、7两个结点就拥有很高的一阶相似度。

二阶相似度：二阶相似度用一句俗话来概括就是“我朋友的朋友也可能是我的朋友”。
他所比较的是两个结点邻居的相似程度。若两个结点拥有相同的邻居，他们也更加的相似。
如果将邻居看作context，那么两个二阶相似度高的结点之间拥有相似的context。
这一点与DeepWalk的目标一致。例如下图中的5、6两点拥有很高的二阶相似度。
```
LINE的目标就是保留这两种相似度。

&nbsp;
## 三、Node2Vec: Scalable Feature Learning for Networks
**Node2Vec是一份基于DeepWalk的延伸工作，它改进了DeepWalk随机游走的策略**。

**Node2Vec认为，现有的方法无法很好的保留网络的结构信息**，例如下图所示，有一些点之间的连接非常紧密(比如u, s1, s2, s3, s4)，他们之间就组成了一个社区(community)。   
网络中可能存在着各种各样的社区，而有的结点在社区中可能又扮演着相似的角色(比如u与s6)。

Node2Vec的优化目标为以下两个：
```
让同一个社区内的结点表示能够相互接近，或

在不同社区内扮演相似角色的结点表示也要相互接近。
```

为此，Node2Vec就要在DeepWalk现有的基础上，对随机游走的策略进行优化。Node2Vec提出了两种游走策略：
```
广度优先策略
深度优先策略
```

&nbsp;
## 四、SDNE: Structural Deep Network Embedding
LINE中提到了一阶相似度以及二阶相似度的概念，那么有没有什么办法，通过深度学习，直接将这两种相似度保留在Embedding中的方法呢？

SDNE提出了这样一个框架，能够使用深度自编码器，在训练的过程中，同时获得节点的一阶相似度和二阶相似度。   
首先，上面提到了二阶相似度其实是节点邻域的相似程度。换句话说，这样的相似度，其实就直接包含在邻接矩阵S的每一行中。所以SDNE直接使用一个深度自编码器，学习网络邻接矩阵的编码与重构，这样一来，二阶相似度就被保留在了Embedding中，训练相对应的损失函数为

接下来就是针对一阶相似度的损失函数了，其实很直接，因为我们最终是将自编码器的隐层当作最终的节点Embedding，所以可以直接像LINE里一样，直接使存在边的两个节点的Embedding相互接近就行了，具体的损失函数如下

&nbsp;
## 五、HARP: Hierarchical Representation Learning for Networks
上面介绍了DeepWalk、LINE、Node2Vec三个网络表示学习中最为著名的三种算法。但是，这三个算法都有一个共同的弱点，那就是他们所捕捉的网络结点关系过近，都是局部邻居。  
LINE仅仅只考虑到了一阶邻居与二阶邻居之间的相似度关系。DeepWalk与Node2Vec虽然可以通过随机游走，获得较长的游走序列，但是游走的长度比起现在图数据的规模，随机游走的长度还是太短了。  
所以就需要想一种方法捕捉全局的相似度。

**HARP采取的方式就是通过多次折叠，将原来的大图层层收缩为较小的图，使得通过较短的随机游走距离，就能够覆盖所有的网络结点，然后将小图作为DeepWalk、LINE或Node2Vec的输入，学习Embedding**。   
最后将在收缩后的小图中学习到的Embedding作为折叠前的的图的Embedding的初始化，继续学习折叠前的图的Embedding。以此类推层层学得原来大图的Embedding。HARP的算法流程如下图所示。

&nbsp;
## 六、GraphGAN: Graph Representation Learning with Generative Adversarial Nets
GraphGAN这份工作，顾名思义，就是尝试使用生成对抗网络(Generative Adversarial Nets, GAN)来增强网络表示学习的表现。

要是理解GAN，那么GraphGAN的大题模块及其作用应该就能够很容易地想到。GraphGAN一共包含两个主要的模块：

### 判别器
由于Network Embedding任务大多是无监督，结点本身不带有任何标签，所以判别器要判别的就是网络自身带有的信息，也就是连接信息。  
GraphGAN中的判别器实际上就是一个连接预测器，通过输入两个结点的Embedding来判定两个结点之间是否存在一条边。

### 生成器
生成器的作用就是通过合理的方式生成一些点对，然后将这些点对输入到判别器中，尽量让判别器将这两个点对误认为是在图上存在边的两个结点的Embedding。  
之后的训练过程就是生成器与判别器较量的过程。生成器不断调整每个结点的Embedding以及自己的参数，使得按照自己的规则生成出来的两个就节点对能够被判别器判别成为存在连接。   
而判别器则通过调整自身的参数，使自己能够准确的将图中实际存在边的结点对判定为true而将生成器生成的结点对判定为false。他们两个共同组成GraphGAN的目标函数：

&nbsp;
## References
[Graph Embedding](https://www.bookstack.cn/read/huaxiaozhuan-ai/2541a32ee733af23.md)   
[网络表示学习(一)--DeepWalk、LINE、Node2Vec、HARP、GraphGAN](https://zhuanlan.zhihu.com/p/64991884)
