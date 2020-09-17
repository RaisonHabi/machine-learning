### 广义注意力机制
在谈论self attention之前我们首先认识一下以KQV模型来解释的Attention机制。

假定输入为Q(Query), Memory中以键值对(K,V)形式存储上下文。那么注意力机制其实是Query到一系列键值对(Key, Value)上的映射函数。

Attention本质上是为序列中每个元素都分配一个权重系数，这也可以理解为软寻址。如果序列中每一个元素都以(K,V)形式存储，那么attention则通过计算Q和K的相似度来完成寻址。
Q和K计算出来的相似度反映了取出来的V值的重要程度，即权重，然后加权求和就得到了attention值。
### Self Attention
Self Attention机制在KQV模型中的特殊点在于Q=K=V，这也是为什么取名self attention，因为其是文本和文本自己求相似度再和文本本身相乘计算得来。
### Self Attention机制的优越之处
RNN本身对于长距离的依赖关系有一定的捕捉能力，但由于序列模型是通过门控单元使得信息保持流动，并且选择性地传递信息。  
但这种方式在文本长度越来越长的条件下，捕捉依赖关系的能力越来越低，因为每一次递归都伴随着信息的损耗，所以有了Attention机制来增强对我们所关注的那部分依赖关系的捕捉。  
除此之外，序列模型也不能对层次结构的信息进行有效的表达。

Attention(包括self attention在内)本身的优点(相较于RNN而言)：
```
对长期依赖关系有着更强的捕捉能力
可以并行计算
```
### CNN
CNN在NLP领域也有比较广泛的应用。  
CNN模型可以被看作n-gram的detector，n-gram的n对应CNN卷积核的大小。   
CNN基于的假设是局部信息存在相互依赖关系，而卷积核可以把这些依赖关系以类似于n-gram的形式提取出来。   
另外，CNN具备Hierachicial Receptive Filed，使得任意两个位置之间的长度距离是对数级别。

Self-Attention的优点(相较于CNN而言)：
```
元素与元素之间的距离从CNN的logarithmic path length进一步缩短到constant path length

由CNN fixed size perceptive变成了variable-sized的 perceptive，具体的长度等于文本长度，这也是self-attention相对于普通attention的优点。
```

所以self-attention相较于Seq2Seq attention还有另一个优点：
```
一步矩阵计算得到了文本序列中任意两个元素的相似度，而且是以整个文本作为观察范围的。
```
### Self-attention的优点归纳为以下：
```
对长期依赖关系有着更强的捕捉能力
可以并行计算
variable-sized的 perceptive，具体的长度等于文本长度，这也是self-attention相对于普通attention的优点。
```

&nbsp;
## reference
[Self Attention 自注意力机制](https://cloud.tencent.com/developer/article/1600550)
