## multi_hot
在做用户画像或为用户做兴趣标签的时候，往往会遇到这样的问题，就是multi-hot特征的处理。  
multi-hot编码之后每个id对应的是多个的1，而且不同样本中1的个数还不一样。  
对multi-hot特征的处理无非也是一种稀疏矩阵的降维压缩，因此可以使用embedding的方法。  

对于某个属性对应的分类特征,可能该特征下有多个取值,比如一个特征表示对哪些物品感兴趣,那么这个特征不是单个值,而是有多个取值,  
样本1 在该属性下取值有1,2两种特征,  样本2 在该属性下有2一种特征, 样本3 在该属性下有3,4 两种特征,  
如果以类似one-hot编码的形式来定义特征应为样本1 [1,1,0,0]  样本2 [0,1,0,0], 样本3 [0,0,1,1],  
但是这种变量不能够直接用embedding_lookup去做,embedding_lookup只接受只有一个1的one-hot编码,那么为了完成这种情况的embedding需要两个步骤:
```
1. 将输入属性转化为类型one-hot编码的形式, 在tensorflow中这个过程是通过tf.SparseTensor来完成的,
实际上就是构建了一个字典矩阵,key为坐标,value为1或者0表示是否有值,
对于一个样本如样本1来说就是构建了一个矩阵[[1,1,0,0]]表示有物品1和2,这个矩阵的规模为[batch_size,num_items],这个过程即为multi-hot编码

2. 将构建好的类似于one-hot编码的矩阵与embedding矩阵相乘,
embedding矩阵的规模为[num_items, embedding_size],
相乘之后得到的输出规模为[batchi_size, embedding_size],即对多维多属性的特征构建了embedding vector
```

&nbsp;
## reference
[multi-hot编码原理](https://blog.csdn.net/weixin_40314737/article/details/81737101)
