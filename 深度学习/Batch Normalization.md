### 0.Batch Norm原理
内部协转移（**Internal Covariate Shift）：由于训练时网络参数的改变导致的网络层输出结果分布的不同。这正是导致网络训练困难的原因**。

对输入进行白化（**whiten：0均值，单位标准差，并且decorrelate去相关**）已被证明能够加速收敛速度，参考Efficient backprop. （LeCun et al.1998b）和 A convergence anal-ysis of log-linear training.（Wiesler & Ney,2011）。
**由于白化中去除相关性类似PCA等操作，在特征维度较高时计算复杂度较高，因此提出了两种简化方式**：
```
1）对特征的每个维度进行标准化，忽略白化中的去除相关性；
2）在每个mini-batch中计算均值和方差来替代整体训练集的计算。
```
batch normalization中即使不对每层的输入进行去相关，也能加速收敛。通俗的理解是在网络的每一层的输入都先做标准化预处理(公式中k为通道channel数),再向整体数据的均值方差方向转换.   
...   
其中ϵ是为了防止方差为0导致数值计算的不稳定而添加的一个小数，如1e−6。

原文: https://www.cnblogs.com/makefile/p/batch-norm.html 

&nbsp;
### 1.BN训练和测试时的参数是一样的嘛？
对于BN，在训练时，是对每一批的训练数据进行归一化，也即用每一批数据的均值和方差。

**而在测试时，比如进行一个样本的预测，就并没有batch的概念，因此，这个时候用的均值和方差是全量训练数据的均值和方差，这个可以通过移动平均法求得**。

对于BN，当一个模型训练完成之后，它的所有参数都确定了，包括均值和方差，gamma和bata。

&nbsp;
### 2.BN训练时为什么不用全量训练集的均值和方差呢？
因为用全量训练集的均值和方差容易过拟合，对于BN，其实就是对每一批数据进行归一化到一个相同的分布，而每一批数据的均值和方差会有一定的差别，
而不是用固定的值，这个差别实际上能够增加模型的鲁棒性，也会在一定程度上减少过拟合。

也正是因此，BN一般要求将训练集完全打乱，并用一个较大的batch值，否则，一个batch的数据无法较好得代表训练集的分布，会影响模型训练的效果。

&nbsp;
### 3.Dropout
Dropout 是在训练过程中以一定的概率的使神经元失活，即输出为0，以提高模型的泛化能力，减少过拟合。

#### Dropout 在训练和测试时都需要嘛？
Dropout 在训练时采用，是为了减少神经元对部分上层神经元的依赖，类似将多个不同网络结构的模型集成起来，减少过拟合的风险。

而在测试时，应该用整个训练好的模型，因此不需要dropout。

#### Dropout 如何平衡训练和测试时的差异呢？
Dropout ，在训练时以一定的概率使神经元失活，实际上就是让对应神经元的输出为0

**假设失活概率为 p** ，就是这一层中的每个神经元都有p的概率失活，如下图的三层网络结构中，如果失活概率为0.5，则平均每一次训练有3个神经元失活，
所以输出层每个神经元只有3个输入，而实际测试时是不会有dropout的，输出层每个神经元都有6个输入，这样在训练和测试时，输出层每个神经元的输入和的期望会有量级上的差异。

**因此在训练时还要对第二层的输出数据除以（1-p）之后再传给输出层神经元，作为神经元失活的补偿，以使得在训练时和测试时每一层输入有大致相同的期望**。

&nbsp;
### 4.BN和Dropout共同使用时会出现的问题
**BN和Dropout单独使用都能减少过拟合并加速训练速度，但如果一起使用的话并不会产生1+1>2的效果，相反可能会得到比单独使用更差的效果**。

相关的研究参考论文：Understanding the Disharmony between Dropout and Batch Normalization by Variance Shift

&nbsp;
### 5.BN层放在ReLU前面还是后面？
原paper建议将BN层放置在ReLU前，因为ReLU激活函数的输出非负，不能近似为高斯分布。

实验表明，放在前后的差异似乎不大，甚至放在ReLU后还好一些。

放在ReLU后相当于直接对每层的输入进行归一化，如下图所示，这与浅层模型的Standardization是一致的。

&nbsp;
### 6.Batch Norm优点
```
减轻过拟合

改善梯度传播（权重不会过高或过低）

容许较高的学习率，能够提高训练速度。

减轻对初始化权重的强依赖，使得数据分布在激活函数的非饱和区域，一定程度上解决梯度消失问题。

作为一种正则化的方式，在某种程度上减少对dropout的使用。
```

&nbsp;
## reference
[BN和Dropout在训练和测试时的差别](https://zhuanlan.zhihu.com/p/61725100)   
[Batch Normalization在测试和训练阶段的不同](https://blog.csdn.net/OliverkingLi/article/details/95970463)   
[深度学习网络层之 Batch Normalization](https://www.cnblogs.com/makefile/p/batch-norm.html)  
[Batch Normalization详解](https://www.cnblogs.com/shine-lee/p/11989612.html)    
[详解深度学习中的Normalization，BN/LN/WN](https://zhuanlan.zhihu.com/p/33173246)  
