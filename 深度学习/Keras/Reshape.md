## Reshape
keras.layers.Reshape(target_shape)  
将输入重新调整为特定的尺寸。  
## 参数
target_shape: 目标尺寸。整数元组。 不包含表示批量的轴。
## 输入尺寸
任意，尽管输入尺寸中的所有维度必须是固定的。 当使用此层作为模型中的第一层时， 使用参数 input_shape （整数元组，不包括样本数的轴）。
## 输出尺寸
(batch_size,) + target_shape
## 例
#作为 Sequential 模型的第一层  
model = Sequential()  
model.add(Reshape((3, 4), input_shape=(12,)))  
#现在：model.output_shape == (None, 3, 4)
#注意： `None` 是批表示的维度

#作为 Sequential 模型的中间层
model.add(Reshape((6, 2)))  
#现在： model.output_shape == (None, 6, 2)

#还支持使用 `-1` 表示维度的尺寸推断  
model.add(Reshape((-1, 2, 2)))  
#现在： model.output_shape == (None, 3, 2, 2)

## reference
[福利 | Keras入门之——网络层构造](https://cloud.tencent.com/developer/article/1111378)  
[核心网络层](https://keras.io/zh/layers/core/)
