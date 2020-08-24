官方文档给出的[LSTM函数](https://keras.io/zh/layers/recurrent/#lstm)的使用规则参考价值不大，因为参量过于复杂，而且解释很少，参考几个开源代码，总结出如下的几种使用方法
## 一、keras LSTM函数使用实例和注释
### 用法1： LSTM(output_dim=CELL_SIZE, input_dim=INPUT_SIZE, input_length=TIME_STEPS, return_sequences=Flase，stateful=FALSE)
```
output_dim：输出单个样本的特征值的维度

input_dim： 输入单个样本特征值的维度

input_length： 输入的时间点长度

return_sequences：布尔值，默认False，控制返回类型。
若为True则返回整个序列，否则仅返回输出序列的最后一个输出，
即当return_sequences取值为True时，网络输入和输出的时间长度TIME_STEPS保持不变，
而当return_sequences取值为FALSE时，网络输出的数据时间长度为1。例如输入数据时间长度为5，输出为一个结果。

stateful： 布尔值，默认为False，
若为True，则一个batch中下标为i的样本的最终状态将会用作下一个batch同样下标的样本的初始状态。
当batch之间的时间是连续的时候，就需要stateful取True，这样batch之间时间连续。
```
实例程序如下：
```
model = Sequential()
model.add(LSTM(32, input_dim=64, input_length=10, return_sequences=True))
# note that you only need to specify the input size on the first layer.
# for subsequent layers, no need to specify the input size:
model.add(LSTM(16, return_sequences=True))
model.add(LSTM(10))
```
上面的程序构建的网络，输入数据的形状为10个时间长度，每一个时间点下的样本数据特征值维度是64。   
第一层LSTM 输出的数据，时间维度仍然是10，每一个时间点下的样本数据特征值维度是32。   
第二层LSTM 输出的数据，时间维度仍然是10，每一个时间点下的样本数据特征值维度是16。   
第三层LSTM 输出的数据，**由于return_sequences默认为FALSE，时间维度变成1**，每一个时间点下的样本数据特征值维度是10。

### 用法2： LSTM(output_dim=CELL_SIZE, batch_input_shape=(BATCH_SIZE, TIME_STEPS, INPUT_SIZE) return_sequences=Flase)
用法2相比于用法1在输入数据中多指定了BATCH_SIZE的大小，但实际使用的时候不太方便，所以本人更倾向于用法1，然后BATCH_SIZE在训练的时候在函数model.fit中指定，如下：
```
model.fit(
    X_train,
    y_train,
    batch_size=512,
    nb_epoch=1,
    validation_split=0.05)
```


&nbsp;
## 二、股票预测实例和陷阱
详见参考文档

&nbsp; 
## reference
[keras LSTM网络的实例和解释](https://blog.csdn.net/yangyuwen_yang/article/details/82218100)
