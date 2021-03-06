Crf具有表达长距离依赖性和交叠性特征的能力，且所有特征可进行全局归一化，能求得全局最优解.
### 1.lr和crf
条件随机场可以看做是逻辑回归的序列化版本，逻辑回归是用于分类的对数线性模型，条件随机场是用于序列化标注的对数线性模型，可以看做是考虑了上下文的分类模型。   
[深度学习在搜索业务中的探索与实践](https://tech.meituan.com/2019/01/10/deep-learning-in-meituan-hotel-search-engine.html)

### 2.crf
条件随机场（conditional random field，简称 CRF），是一种鉴别式几率模型，是随机场的一种，常用于标注或分析序列资料，如自然语言文字或是生物序列。

如同马尔可夫随机场，**条件随机场为无向性之图模型，图中的顶点代表随机变量，顶点间的连线代表随机变量间的相依关系**，在条件随机场当中，随机变量 Y 的分布为条件几率，给定的观察值则为随机变量 X。原则上，条件随机场的图模型布局是可以任意给定的，一般常用的布局是链接式的架构，链接式架构不论在训练（training）、推论（inference）、或是解码（decoding）上，都存在有效率的算法可供演算。  
[条件随机场](https://zh.wikipedia.org/wiki/%E6%A2%9D%E4%BB%B6%E9%9A%A8%E6%A9%9F%E5%9F%9F)
### 3.
CRF是给定一组输入随机变量条件下另一组输出随机变量的条件概率分布模型，其特点是**假设输出随机变量构成马尔可夫（Markov）随机场**。  
[NLP入门（八）使用CRF++实现命名实体识别(NER)](https://www.cnblogs.com/jclian91/p/10795413.html)
### 4.逐帧softmax和crf（详见图片区别）
CRF 主要用于序列标注问题，可以简单理解为是给序列中的每一帧都进行分类，既然是分类，很自然想到将这个序列用 CNN 或者 RNN 进行编码后，接一个全连接层用 softmax 激活，如下图所示：  

然而，当我们设计标签时，比如用 s、b、m、e 的 4 个标签来做字标注法的分词，目标输出序列本身会带有一些上下文关联，比如 s 后面就不能接 m 和 e，等等。逐标签 softmax 并没有考虑这种输出层面的上下文关联，所以它意味着把这些关联放到了编码层面，希望模型能自己学到这些内容，但有时候会“强模型所难”。 

而 CRF 则更直接一点，它**将输出层面的关联分离了出来**，这使得模型在学习上更为“从容”：

当然，如果仅仅是引入输出的关联，还不仅仅是 CRF 的全部，CRF 的真正精巧的地方，是它以路径为单位，考虑的是路径的概率。 

假如一个输入有 n 帧，每一帧的标签有 k 中可能性，那么理论上就有k^n中不同的输入。我们可以将它用如下的网络图进行简单的可视化。在下图中，每个点代表一个标签的可能性，点之间的连线表示标签之间的关联，而每一种标注结果，都对应着图上的一条完整的路径。

而在序列标注任务中，我们的正确答案是一般是唯一的。比如“今天天气不错”，如果对应的分词结果是“今天/天气/不/错”，那么目标输出序列就是 bebess，除此之外别的路径都不符合要求。

换言之，在序列标注任务中，我们的研究的基本单位应该是路径，我们要做的事情，是从 k^n 条路径选出正确的一条，那就意味着，如果将它视为一个分类问题，那么将是 k^n 类中选一类的分类问题。

#### 这就是逐帧 softmax 和 CRF 的根本不同了：前者将序列标注看成是 n 个 k 分类问题，后者将序列标注看成是 1 个 k^n 分类问题
[简明条件随机场CRF介绍 | 附带纯Keras实现](https://www.jiqizhixin.com/articles/2018-05-23-3)
