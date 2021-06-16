## 一、wide&deep
### 0.思路：提供了线性模型或树模型和nn融合的方式

### 1.linear models
Generalized linear models with nonlinear feature transformations are widely used for large-scale regression and classification problems with sparse inputs.   
Memorization of feature interactions through a wide set of cross-product feature
transformations are effective and interpretable, while **generalization requires more feature engineering effort**.
### 2.deep neural networks
With less feature engineering, deep neural networks can generalize better to unseen feature combinations through low-dimensional
dense embeddings learned for the sparse features.   
However,**deep neural networks with embeddings can over-generalize
and recommend less relevant items when the user-item interactions are sparse and high-rank**. 
### 3.Wide & Deep
Wide & Deep learning—jointly trained wide linear models
and deep neural networks—to combine the benefits of memorization and generalization for recommender systems
### 4.总结
Memorization and generalization are both important for recommender systems.  
**Wide linear models can effectively memorize sparse feature interactions using cross-product feature transformations**,   
while **deep neural networks can generalize to previously unseen feature interactions through lowdimensional embeddings**. 

We presented the Wide & Deep learning framework to combine the strengths of both types of model.

### 5.Joint Training of Wide & Deep Model
In the experiments, we used Followthe-regularized-leader (FTRL) algorithm [3] with L1 regularization as the optimizer for the wide part of the model,
and AdaGrad [1] for the deep part.

&nbsp;
## 二、wide&deep论文解读(特征处理详见模型结构图)
[详解 Wide & Deep 结构背后的动机](https://zhuanlan.zhihu.com/p/53361519)

### 0.核心思想
Wide & Deep 模型的核心思想是结合线性模型的记忆能力和 DNN 模型的泛化能力，从而提升整体模型性能。
### 1.Motivation
推荐系统的主要挑战之一，是同时解决Memorization和Generalization，理解这两个概念是理解全文思路的关键，下面分别进行解释。
### 1.1 Memorization
面对拥有**大规模离散sparse特征的CTR预估问题时，将特征进行非线性转换，然后再使用线性模型是在业界非常普遍的做法，最流行的即「LR+特征叉乘」**。   
**Memorization 通过一系列人工的特征叉乘（cross-product）来构造这些非线性特征，捕捉sparse特征之间的高阶相关性，即“记忆” 历史数据中曾共同出现过的特征对**。

典型代表是LR模型，使用大量的原始sparse特征和叉乘特征作为输入，**很多原始的dense特征通常也会被分桶离散化构造为sparse特征**。   
这种做法的**优点是模型可解释高，实现快速高效，特征重要度易于分析，在工业界已被证明是很有效的**。   
Memorization的缺点是：
```
需要更多的人工设计；

可能出现过拟合。
可以这样理解：如果将所有特征叉乘起来，那么几乎相当于纯粹记住每个训练样本，
这个极端情况是最细粒度的叉乘，我们可以通过构造更粗粒度的特征叉乘来增强泛化性；

无法捕捉训练数据中未曾出现过的特征对。
例如上面的例子中，如果每个专业的人都没有下载过《消愁》，那么这两个特征共同出现的频次是0，模型训练后的对应权重也将是0；
```
### 1.2 Generalization
Generalization 为sparse特征学习低维的dense embeddings 来捕获特征相关性，学习到的embeddings 本身带有一定的语义信息。  
可以联想到NLP中的词向量，不同词的词向量有相关性，因此文中也称Generalization是基于相关性之间的传递。这类模型的代表是DNN和FM。

Generalization的**优点是更少的人工参与，对历史上没有出现的特征组合有更好的泛化性** 。   
但在推荐系统中，**当user-item matrix非常稀疏时，例如有和独特爱好的users以及很小众的items，NN很难为users和items学习到有效的embedding**。  
**这种情况下，大部分user-item应该是没有关联的，但dense embedding 的方法还是可以得到对所有 user-item pair 的非零预测，因此导致 over-generalize并推荐不怎么相关的物品**。  
**此时Memorization就展示了优势，它可以“记住”这些特殊的特征组合**。

#### Memorization根据历史行为数据，产生的推荐通常和用户已有行为的物品直接相关的物品。而Generalization会学习新的特征组合，提高推荐物品的多样性。 

### 2.模型结构
Wide & Deep模型结合了LR和DNN，Wide 该部分是广义线性模型  
Deep 部分是前馈神经网络，网络会**对一些sparse特征（如ID类特征）学习一个低维的dense embeddings（维度量级通常在O(10)到O(100)之间），然后和一些原始dense特征一起作为网络的输入**。

损失函数 模型选取logistic loss作为损失函数，
### 3.联合训练
联合训练（Joint Training）和集成（Ensemble）是不同的，  
集成是每个模型单独训练，再将模型的结果汇合。  
相比联合训练，集成的每个独立模型都得学得足够好才有利于随后的汇合，因此每个模型的model size也相对更大。  
而联合训练的wide部分只需要作一小部分的特征叉乘来弥补deep部分的不足，不需要 一个full-size 的wide 模型。

在论文中，作者通过梯度的反向传播，使用 mini-batch stochastic optimization 训练参数，并对wide部分使用带L1正则的Follow- the-regularized-leader (FTRL) 算法，对deep部分使用 AdaGrad算法。

### 4.实验细节（详见参考文档的图）
```
Categorical 特征（sparse）会有一个过滤阈值，即至少在训练集中出现m次才会被加入

Continuous 特征（dense）通过CDF被归一化到 [0,1] 之间

Categorical 特征映射到32维embeddings，和原始Continuous特征共1200维作为NN输入

Wide部分只用了一组特征叉乘，即被推荐的app ☓ 用户下载的app

线上模型更新时，通过“热启动”重训练，即使用上次的embeddings和模型参数初始化
```
Wide部分设置很有意思，作者为什么这么做呢？结合业务思考......     
对于Wide来说，它现在的任务是弥补Deep的缺陷，其他大部分的活就交给Deep了，所以这时的Wide相比单独Wide也显得非常“轻量级”，这也是Join相对于Ensemble的优势。

### 附：cdf累积分布函数 (Cumulative distribution function)
累积分布函数，又叫分布函数，是概率密度函数的积分，能完整描述一个实随机变量X的概率分布。一般以大写“CDF”标记。

&nbsp;
## 三、DeepFM
[ctr预估之Wide&Deep和DeepFM](https://blog.csdn.net/Zhangbei_/article/details/90544265)
### 1.模型结构（详见参考文档）
黑色线表示带有权重的连接，红色线表示权重为1的连接，蓝色线表示one-hot映射到对应的embedding。  
**图中的Field表示一个特征类别，比如手机型号是一个field，性别是一个field，地理位置是一个field。每个field用one-hot表示，构成最下面的Sparse Features**。  
稀疏特征有两部分输出，**一部分是加权求和得到FM Layer中的第一个节点，另一部分索引对应的embedding得到Dense Embeddings**。  
Dense Embeddings也有两部分输出，**一部分是两两做点积输出到FM Layer，另外一部分是拼接起来作为Hidden Layer的输入**。
### 2.公式
一部分是因子分解机FM，一部分是DNN，DNN和上面介绍的wide&deep的deep部分是相同的。**需要注意的是，FM和DNN共享embedding**。  
**论文中做过试验，将FM和DNN的embedding相互独立，效果要差**。  
关于DeepFM论文给出的物理意义是，**FM部分负责一阶特征和二阶交叉特征，而DNN部分负责二阶以上的高阶特征交叉**。
### 3.超参调试
```
1）激活函数relu效果好于tanh，这与wide&deep的线上模型一致。
2）每个隐层的unit在200到400之间效果最佳，相比wide&deep的unit数量要少一些。
3）3层网络效果最佳，这与wide&deep的隐层数量相同。
4）在总的神经单元数量固定的情况下，constant结构效果最佳，constant表示每个隐层的unit数量相同；而wide&deep采用的是塔式结构，越往上unit数量越少。
5）文中没有说真实场景中embedding维度是多少，但是试验中的embedding维度设置为10，wide&deep中是32维，两个都不大。
```

&nbsp;
## 四、wide&deep和DeepFM的异同
1）两者的DNN部分模型结构相同/相似；

2）wide&deep需要做特征工程，二阶特征交叉需要靠特征工程来实现，通过wide部分发挥作用；

3）DeepFM完全不需要做特征工程，直接输入原始特征即可，二阶特征交叉靠FM来实现，并且FM和DNN共享embedding；

4）在总的神经单元数量固定的情况下，constant结构效果最佳，constant表示每个隐层的unit数量相同；而wide&deep采用的是塔式结构，越往上unit数量越少（详见各模型结构图）。

5）从试验结果来看DeepFM效果优于wide&deep。

### 1.预测函数
#### 1.1 wide&deep
模型选取logistic loss作为损失函数，此时Wide &amp; Deep最后的预测输出为<p><img src="https://www.zhihu.com/equation?tex=p%28y%3D1%7C%5Cbm%7Bx%7D%29%3D%5Csigma%28%5Cbm%7Bw%7D%5E%7BT%7D_%7Bwide%7D%5B%5Cbm%7Bx%7D%2C%5Cphi%28%5Cbm%7Bx%7D%29%5D%2B%5Cbm%7Bw%7D%5E%7BT%7D_%7Bdeep%7D%5Cbm%7Ba%7D%5E%7Bl_f%7D%2Bb%29" alt="p(y=1|\bm{x})=\sigma(\bm{w}^{T}_{wide}[\bm{x},\phi(\bm{x})]+\bm{w}^{T}_{deep}\bm{a}^{l_f}+b)" eeimg="1"/> </p><p>其中 <img src="https://www.zhihu.com/equation?tex=%5Csigma" alt="\sigma" eeimg="1"/> 表示sigmoid函数， <img src="https://www.zhihu.com/equation?tex=%5Cphi%28%5Cbm%7Bx%7D%29" alt="\phi(\bm{x})" eeimg="1"/> 表示叉乘特征， <img src="https://www.zhihu.com/equation?tex=%5Cbm%7Ba%7D%5E%7Bl_f%7D" alt="\bm{a}^{l_f}" eeimg="1"/> 表示NN最后一层激活值。</p>
#### 1.2 deepfm
y =sigmoid(yFM + YDNN)

### 2.注意
文中提到过field的定义，每一个特征类别叫做field。      
在DeepFM中，文章作者直接明确表示，每个field都是单值的。（参考文档[ctr预估之Wide&Deep和DeepFM](https://blog.csdn.net/Zhangbei_/article/details/90544265)最后作者的疑问，说明对embedding理解有偏差）

&nbsp;
## References
[Wide & Deep Learning for Recommender Systems](https://arxiv.org/abs/1606.07792)   
[详解 Wide & Deep 结构背后的动机](https://zhuanlan.zhihu.com/p/53361519)     
[ctr预估之Wide&Deep和DeepFM](https://blog.csdn.net/Zhangbei_/article/details/90544265)

