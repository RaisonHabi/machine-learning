## 回调函数 Callbacks
回调函数是一个函数的合集，会在训练的阶段中所使用。  
你可以使用回调函数来查看训练模型的内在状态和统计。  
你可以传递一个列表的回调函数（作为 callbacks 关键字参数）到 Sequential 或 Model 类型的 .fit() 方法。  
在训练时，相应的回调函数的方法就会被在各自的阶段被调用。  
[回调函数使用](https://keras.io/zh/callbacks/)
## Model类（函数式 API）
在函数式 API 中，给定一些输入张量和输出张量，可以通过以下方式实例化一个 Model：  
> from keras.models import Model  
from keras.layers import Input, Dense  
a = Input(shape=(32,))  
b = Dense(32)(a)  
model = Model(inputs=a, outputs=b)  
这个模型将包含从 a 到 b 的计算的所有网络层。  
在多输入或多输出模型的情况下，你也可以使用列表：  
model = Model(inputs=[a1, a2], outputs=[b1, b3, b3])  

[Model 类（函数式 API）](https://keras.io/zh/models/model/)  
[Model class API](https://keras.io/models/model/)  
