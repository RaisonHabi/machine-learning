## Embedding
keras.layers.Embedding(input_dim, output_dim, embeddings_initializer='uniform', embeddings_regularizer=None, activity_regularizer=None, embeddings_constraint=None, mask_zero=False, input_length=None)  

将正整数（索引值）转换为固定尺寸的稠密向量。 例如： [[4], [20]] -> [[0.25, 0.1], [0.6, -0.2]]  
该层只能用作模型中的第一层。

## 参数
input_dim: int > 0。词汇表大小， 即，最大整数 index + 1。  
output_dim: int >= 0。词向量的维度。  
embeddings_initializer: embeddings 矩阵的初始化方法 (详见 initializers)。  
embeddings_regularizer: embeddings matrix 的正则化方法 (详见 regularizer)。  
embeddings_constraint: embeddings matrix 的约束函数 (详见 constraints)。  
mask_zero: 是否把 0 看作为一个应该被遮蔽的特殊的 "padding" 值。 
> 这对于可变长的 循环神经网络层 十分有用。   
如果设定为 True，那么接下来的所有层都必须支持 masking，否则就会抛出异常。   
如果 mask_zero 为 True，作为结果，索引 0 就不能被用于词汇表中 （input_dim 应该与 vocabulary + 1 大小相同）。

input_length: 输入序列的长度，当它是固定的时。 
> **如果你需要连接 Flatten 和 Dense 层，则这个参数是必须的 （没有它，dense 层的输出尺寸就无法计算）**。
## reference
[Embedding](https://keras.io/zh/layers/embeddings/)
