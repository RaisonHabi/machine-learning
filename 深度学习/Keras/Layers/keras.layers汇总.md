### 核心网络层
keras.layers.Dense(...):常用的的全连接层   
keras.layers.Activation(activation)   
keras.layers.Dropout(rate, noise_shape=None, seed=None)    
keras.engine.input_layer.Input()   
keras.layers.Masking(mask_value=0.0)   
......

[核心网络层](https://keras.io/zh/layers/core/)  
### 卷积层 Convolutional
keras.layers.Conv1D(...)   
keras.layers.Conv2D(...)    
......

[卷积层 Convolutional](https://keras.io/zh/layers/convolutional/)
### 池化层 Pooling
keras.layers.MaxPooling1D(...):对于时序数据的最大池化   
keras.layers.MaxPooling2D(...):对于空间数据的最大池化   
keras.layers.MaxPooling3D(...):对于 3D（空间，或时空间）数据的最大池化    
......

[池化层 Pooling](https://keras.io/zh/layers/pooling/)
### 局部连接层 Locally-connected
keras.layers.LocallyConnected1D(...)   
keras.layers.LocallyConnected2D(...)

[局部连接层 Locally-connected](https://keras.io/zh/layers/local/)
### 循环层
keras.layers.RNN(...)  
keras.layers.SimpleRNN(...)   
keras.layers.GRU(...)   
keras.layers.LSTM(...)   
keras.layers.ConvLSTM2D(...)  
......


[循环层 Recurrent](https://keras.io/zh/layers/recurrent/)   
### 
[]()
### 
[]()
### 嵌入层
[嵌入层 Embedding](https://keras.io/zh/layers/embeddings/)
### 融合层
keras.layers.Add()  
keras.layers.Subtract()   
keras.layers.Multiply()   
keras.layers.Average()   
keras.layers.Maximum()   
keras.layers.Concatenate(axis=-1)   
keras.layers.Dot(axes, normalize=False)   
keras.layers.add(inputs):Add 层的函数式接口   
keras.layers.subtract(inputs):Subtract 层的函数式接口   
......

[融合层 Merge](https://keras.io/zh/layers/merge/)
### 高级激活层
keras.layers.LeakyReLU(alpha=0.3)   
keras.layers.PReLU(...)   
keras.layers.ELU(alpha=1.0)   
keras.layers.ThresholdedReLU(theta=1.0)   
keras.layers.Softmax(axis=-1)   
keras.layers.ReLU(max_value=None, negative_slope=0.0, threshold=0.0)  

[高级激活层 Advanced Activations](https://keras.io/zh/layers/advanced-activations/)
### 标准化层 Normalization
keras.layers.BatchNormalization(...)

[标准化层 Normalization](https://keras.io/zh/layers/normalization/)
### 噪声层 Noise
keras.layers.GaussianNoise(stddev)   
keras.layers.GaussianDropout(rate)   
keras.layers.AlphaDropout(rate, noise_shape=None, seed=None)

[噪声层 Noise](https://keras.io/zh/layers/noise/)
### 层封装器 wrappers
keras.layers.TimeDistributed(layer)   
keras.layers.Bidirectional(layer, merge_mode='concat', weights=None)   

[层封装器 wrappers](https://keras.io/zh/layers/wrappers/)
### 编写你自己的层
[编写你自己的层](https://keras.io/zh/layers/writing-your-own-keras-layers/)
