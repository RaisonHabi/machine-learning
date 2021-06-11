## 解答一
首先，用来学习item embedding的方法大部分是可以迁移到学习user embedding中去的，因为user和item在推荐系统中处在大致对称的两个位置（在矩阵分解中显得尤为明显）。

但是user和item还是有区别的。从建模的角度来说，item的属性一般是不太随时间变化而变化的，而**user则不同，user的属性和兴趣会动态变化**。  
从目标的角度来说，推荐系统的最终目标是为user推荐item，所以user始终是模型的中心。

想理清楚如何做user embedding其实并不困难。我们对user的所有认知其实只包括两部分的信息：**user的内在属性和user的外部联系**。  
而user的外部联系，又分为user对user的联系和user对item的联系。  
因此，我们对user的刻画也就分成了三部分：
```
1.考虑user的内在属性信息，包括其性别、年龄、地域、学历等。这些信息也叫做demographic information（人口统计学信息，
因为这些就是做人口普查时需要统计的量）。

一般而言，推荐系统模型是将这些属性离散化并拼接起来，作为神经网络的原始输入，参考YoutubeNet[1]。

值得一提的是，某些模型设计了比拼接更复杂的方法，比如在Wide&Deep [2]中，wide component
就设计了一种组合特征（combinatorial feature）。组合特征是指考虑多个单维特征的组合结果。
例如，如果我们有关于用户的gender和language的特征，那么我们可以设计一维新的特征叫gender=F&language=en，
它只有在这个user的gender=F且language=en时才为1。

组合特征可以显式地刻画特征之间的关联，但是需要一些手工设计，并且会增加模型的复杂度。关于组合特征，
可以参考Factorization Machines（分解机）系列工作，如LibFM [3]、DeepFM [4]、xDeepFM [5]等。
```
```
2.考虑user对user的联系。这一块也是推荐系统中的一个大分类，叫social recommendation。

这种方法假设user之间存在一个social network，这其实在实际应用中也是非常常见的，
几乎所有主流的互联网应用都会允许用户之间存在好友或关注的关系。

social network中有一个同质性假设（homophily assumption），也就是相似的人之间会更可能有边连接。

基于这个假设，social recommendation的核心就是使用一个user的邻居信息来推断该user的信息。
一种简单的做法就是让一个user的embedding和他的邻居的embedding相似，
这可以通过在矩阵分解的基础上对一条边两端的user的embedding之间的差值设计一个损失项来实现[6]。
```
```
3.考虑user对item的联系。

因为推荐系统中user-item之间的关系是核心，所以这也是做user embedding要考虑的核心问题。
只要不是一个完全新的user，他就会有自己的点击/观看/收藏/购买历史记录，因此，
这里的问题就变成了如何用user的历史记录来刻画这个user。

一种简单的做法当然是将user的历史记录平均起来，作为这个user的embedding。

但是实际上，我们有一些更好的考量：
(1) 考虑时间因素，刻画用户在时间轴上的变化，这也是推荐系统中的一大类，叫sequential/session-based recommendation [7, 8]，
主要依赖于RNN模型去进行用户建模。
(2）考虑用户兴趣因素，刻画用户对不同topic、style的兴趣差异。这也是最近很多研究的出发点，
按照技术角度来说也叫attention-based recommendation [9]。

另外值得提的两点：
一，在某些工作里是没有user embedding的，user完全由item embedding刻画 [10]。
二，user和item的联系不仅仅只有0/1的交互信息，还有评论等UGC（user generated content），
想用上这些评论，就和sentiment analysis，aspect mining，natural language processing等领域相关，也就此打住。
```
至于推荐系统的上限是否取决于矩阵分解的效果，你可以把矩阵分解当成是一种pre-training，可以在后续模型中对user embedding再进行fine-tuning即可。

[推荐系统中如何做 User Embedding？ - 王鸿伟的回答 - 知乎](https://www.zhihu.com/question/336110178/answer/823523924)

&nbsp;
## 解答二
既然题主是玩Graph方向的，那其实应该就比较好理解了  

#### 将整个推荐系统对应于相应的Graph其中如果作为**同质图**理解的话，那item就是对应的node，user就是对应相应的sentence，类似于采样出来的序列，  
那这个时候用任何可以sentence-embedding的方法都可以获得相应embedding 
```
1.最粗暴的方法是采用item-embedding进行平均，而序列的长度很关键，太短了无法完全表征，
太长了无法可能用户兴趣发生了偏移效果不好，因而可以作为baseline(大多数情况下，效果说的过去)

2.在上面的加上一点点改进，对应不同的推荐商品召回的情况下，采用不同的embedding，或者加上attention-layer，
相当于用户的兴趣是多峰的，每次只有单峰激活(参考DIN)

3.加上序列和时间考量，用LSTM(GRU)等处理这个“sentence”，最终得到的编码可以是user-embedding(DIEN)
```
以上所有的方法都是基于采用item作为sentence进行embedding进行表示的，其思路和nlp中差不多，如果已经有了word embedding， 如何获取sentence embedding。

#### 如果是将整个网络作为**异质图**来理解，user-item分别是其中的 node ，此时可以采用使用随机游走的方式进行获取node-embedding，进而获取 user-embedding，
可以采用的方法就很多了，比如deep-walk based的一系列（DeepWalk，node2vec，etc）。

实际操作的过程中，如果数据量不是十分充足，建起来的图可能表现并不好，更多的是学术的炫技，**工业还是基于sentence或者CF进行处理**。

#### 另外，对于embedding的训练，通常有两种思路
```
直接训练：一种是和上面一样，想办法获取embedding，再把embedding加入到网络模型中喂给最后的推荐系统，end2end的中间产物：

另一种是加入embedding-layer 和 attention-layer，此时的embedding是副产物。
```
我目前的实践上看起来，端到端的更难训，但是最后的表现会稍微好一些。

[推荐系统中如何做 User Embedding？ - Hover的回答 - 知乎](https://www.zhihu.com/question/336110178/answer/778884174)

&nbsp;
## References
[推荐系统中如何做 User Embedding？](https://www.zhihu.com/question/336110178)
