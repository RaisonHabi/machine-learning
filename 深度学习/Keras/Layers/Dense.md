## Dense
keras.layers.Dense(units, activation=None, use_bias=True, kernel_initializer='glorot_uniform', bias_initializer='zeros', kernel_regularizer=None, bias_regularizer=None, activity_regularizer=None, kernel_constraint=None, bias_constraint=None)  
就是常用的的全连接层。  

Dense 实现以下操作： output = activation(dot(input, kernel) + bias)   
其中 activation 是按逐个元素计算的激活函数，kernel 是由网络层创建的权值矩阵，以及 bias 是其创建的偏置向量 (只在 use_bias 为 True 时才有用)。  
***注意: 如果该层的输入的秩大于2，那么它首先被展平然后 再计算与 kernel 的点乘***。  
示例：  
> #作为 Sequential 模型的第一层  
model = Sequential()  
model.add(Dense(32, input_shape=(16,)))  
#现在模型就会以尺寸为 (*, 16) 的数组作为输入，  
#其输出数组的尺寸为 (*, 32)  
#Sequential在第一层之后，就不再需要指定输入的尺寸了：  
model.add(Dense(32))  
## 参数
**units: 正整数，输出空间维度**。  
activation: 激活函数 (详见 activations)。 若不指定，则不使用激活函数 (即，「线性」激活: a(x) = x)。  
use_bias: 布尔值，该层是否使用偏置向量。  
kernel_initializer: kernel 权值矩阵的初始化器 (详见 initializers)。  
bias_initializer: 偏置向量的初始化器 (see initializers).  
kernel_regularizer: 运用到 kernel 权值矩阵的正则化函数 (详见 regularizer)。  
bias_regularizer: 运用到偏置向的的正则化函数 (详见 regularizer)。  
activity_regularizer: 运用到层的输出的正则化函数 (它的 "activation")。 (详见 regularizer)。  
kernel_constraint: 运用到 kernel 权值矩阵的约束函数 (详见 constraints)。  
bias_constraint: 运用到偏置向量的约束函数 (详见 constraints)。  

## 输入尺寸
nD 张量，尺寸: (batch_size, ..., input_dim)。   
最常见的情况是一个尺寸为 (batch_size, input_dim) 的 2D 输入。

## 输出尺寸
nD 张量，尺寸: (batch_size, ..., units)。   
例如，对于尺寸为 (batch_size, input_dim) 的 2D 输入， 输出的尺寸为 (batch_size, units)。



## reference
[Dense](https://keras.io/zh/layers/core/)
