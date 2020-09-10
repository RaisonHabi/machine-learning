## 优化
模型输出   【公式】    
wide使用Follow-the-regularized-leader (FTRL)+L1正则 FTRL优化算法   
deep使用AdaGrad
注意这里的同时优化，google论文中叫joint training(联合训练)   
并非ensemble(集成学习)
这是因为对于集成学习，每个子学习器的训练都是独立的，两个子学习器参数不会一起训练。
而联合训练指W_{wide},W_{deep}参数是同时更新的。
## Embedding 层
### 为什么需要做embedding？
超高维度的稀疏输入输入网络，将带来更高维度的参数矩阵，这会带来更大的计算压力。  
所以神经网络更善于处理稠密的实值输入。所以，需要对稀疏的离散特征做embedding
### 怎么做embedding？
**1，离线提前做embedding，例如对于词的嵌入可以使用Word2vec对词做嵌入。也可利用FM先学习好稀疏特征的隐向量**。

2，随机初始化。之后跟着模型参数一起训练。其实1中无论是word2vec还是FM，也是一开始随机初始化，然后训练学习而来。这是最常用的方法。

在wide&deep中，embedding是随机生成的，并在接下来的训练中更新
```
The embedding values are initialized randomly, 
and are trained along with all other model parameters to minimize the training loss.

tf.feature_column.embedding_column(categorical_column,
                     dimension,
                     combiner='mean',
                     initializer=None,
                     ckpt_to_load_from=None,
                     tensor_name_in_ckpt=None,
                     max_norm=None,
                     trainable=True)
                     
注意参数trainable=True即默认会继续训练这个embedding
而当设置trainable=False时，一般配合已经训练好的embedding                   
```
## 讨论
### wide与deep分别代表了什么？
wide是简单的线性模型，**他会记住训练数据中已经出现的模式，并赋予权重。这代表了记忆** 

deep是深度的复杂模型，会**在一层层的网络中计算出训练数据中未出现的模式的权重。这代表了泛化**

这里的模式，可以简单理解为特征组合。
```
Wide侧就是普通LR，一般根据人工先验知识，将一些简单、明显的特征交叉，喂入Wide侧，让Wide侧能够记住这些规则。

Deep侧就是DNN，通过embedding的方式将categorical/id特征映射成稠密向量，
让DNN学习到这些特征之间的深层交叉，以增强扩展能力。
```
### 但其实deep模型本身也会记住已出现的模式并进行训练吧？相当于低阶特征也可以得到有效利用，为什么还要加上wide模型呢？
可能原因：deep模型可解释性不强。wide模型可解释性强。    
通过wide模型可以挑选出权重较高的低阶特征。同时，对低阶特征另外单独建模，也是很有可能提高精度的。
### 其他
相比于实数型特征，稀疏的类别/ID类特征，才是推荐、搜索领域的“一等公民”，被研究得更多。  
即使有一些实数值特征，比如历史曝光次数、点击次数、CTR之类的，也往往通过bucket的方式，变成categorical特征，才喂进模型。



&nbsp;
## reference
[推荐系统-重排序-CTR-Wide&Deep模型](https://www.jianshu.com/p/56c0d94214d7)
