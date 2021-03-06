## 隐含狄利克雷分布
LDA首先由Blei, David M.、吴恩达和Jordan, Michael I于2003年提出
### 1.模型
隐含狄利克雷分布（Latent Dirichlet Allocation，LDA），是一种主题模型（topic model），它可以将文档集中每篇文档的主题按照概率分布的形式给出。

LDA是一种典型的词袋模型，即它认为一篇文档是由一组词构成的一个集合，词与词之间没有顺序以及先后的关系。一篇文档可以包含多个主题，文档中每一个词都由其中的一个主题生成。

另外，**正如Beta分布是二项式分布的共轭先验概率分布，狄利克雷分布作为多项式分布的共轭先验概率分布**。  
因此正如LDA贝叶斯网络结构中所描述的，在LDA模型中一篇文档生成的方式如下: 详见参考文档

### 2.性质
LDA是常见的主题模型之一，是一类无监督学习算法，**在训练时不需要手工标注的训练集，需要的仅仅是文档集以及指定主题的数量k即可**。此外LDA的另一个优点则是，对于每一个主题均可找出一些词语来描述它。
### 3.应用
LDA在自然语言处理领域，包括文本挖掘（text mining）及其下属的文本主题识别、文本分类以及文本相似度计算方面有应用。

**解释：**
```
共有m篇文档，k个主题，每篇文档（长度为Nm）都有各自的主题分布比方说40%的武侠、30%爱情、20%科幻、其余的加起来为10%。
这个分布是一个多项分布，该多项分布的参数服从Dirichlet分布，该Dirichlet的参数为α。

每个主题又有各自的词分布，词分布同样是多项分布，参数服从Dirichlet分布，参数为β。

对于每篇文档中的第n个词的处理，首先从文档中的主题分布采样一个主题，然后在这主题的词分布中采样一个词，不断重复上述过程，直到m篇文档都完成。

那么主题和词是怎么采的呢？
这里先简单介绍一下Gibbs Sampling的过程，后边的采样文章会深入的介绍采样。
```


### 4.注意
LDA对于短的文章来说效果不是很好，因为还没等收敛就已经结束了，我们可以通过短文拼接来改善。

&nbsp;
## 共轭分布、Gibbs采样
### 1.共轭分布
```
共轭（conjugate）是贝叶斯方法中很常见的一个词，结合贝叶斯定理，我们可以将“共轭”理解为后验和先验是同一种分布。  
在贝叶斯的理论体系中，如果先验概率分布和后验概率分布满足同样的分布律的话，我们就说先验分布和后验分布是共轭分布，同时，先验分布又叫做似然函数的共轭先验分布。
大白话来说就是：如果一个概率分布Z乘以一个分布Y之后的分布仍然是Z，那么就是共轭分布，仅此而已。
```
### 2.Gibbs采样
```
Gibbs Sampling算法的运行方式是每次概率向量的一个维度，给定其他维度的变量值采样当前维度的值，不断迭代直到收敛输出待估计的参数。

Gibbs在LDA中，初始时随机给文本中的每个词分配主题z0，然后统计每个主题出现的词t的数量以及每个文档m下出现的主题z的数量，
每一轮计算p(zi|z-i,d,w)，即排除当前词的主题分布，根据其他所有词的主题分布估计当前词分配各个主题的概率。

当得到当前词属于所有主题z的概率分布后，根据这个概率分布为该次采样一个新的主题。

用同样方法更新下一个词的主题，知道发现每个文档的主题分布θi和每个主题的词分布φj收敛，算法终止，输出待估计的参数θ和φ，同时每个单词的主题Zmn也可以得到。
```
原文链接：https://blog.csdn.net/u012771351/java/article/details/53032365

吉布斯采样（英语：Gibbs sampling）是统计学中用于马尔科夫蒙特卡洛（MCMC）的一种算法，用于在难以直接采样时从某一多变量概率分布中近似抽取样本序列。该序列可用于近似联合分布、部分变量的边缘分布或计算积分（如某一变量的期望值）。某些变量可能为已知变量，故对这些变量并不需要采样。

吉布斯采样常用于统计推断（尤其是贝叶斯推断）之中。这是一种随机化算法，与最大期望算法等统计推断中的确定性算法相区别。与其他MCMC算法一样，吉布斯采样从马尔科夫链中抽取样本，可以看作是Metropolis–Hastings算法的特例。

算法描述详见：[吉布斯采样](https://zh.wikipedia.org/wiki/%E5%90%89%E5%B8%83%E6%96%AF%E9%87%87%E6%A0%B7)

&nbsp;
## reference
[LDA-隐狄利克雷分布-主题模型](https://blog.csdn.net/u012771351/article/details/53032365)   
[隐含狄利克雷分布](https://baike.baidu.com/item/%E9%9A%90%E5%90%AB%E7%8B%84%E5%88%A9%E5%85%8B%E9%9B%B7%E5%88%86%E5%B8%83/15716062)
