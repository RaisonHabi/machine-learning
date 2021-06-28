## 简 介
简单的模型例如线性回归，LR等模型非常易于解释，但在实际应用中的效果却远远低于复杂的梯度提升树模型以及神经网络等模型。现在大部分互联网公司的建模都是基于梯度提升树或者神经网络模型等复杂模型，遗憾的是，这些模型虽然效果好，但是我们却较难对其进行很好地解释，这也是目前一直困扰着大家的一个重要问题，现在大家也越来越加关注模型的解释性。

本文介绍一种解释机器学习模型输出的方法LIME。

&nbsp;
## LIME
LIME（Local Interpretable Model-agnostic Explanations）支持的模型包括：
```
结构化模型的解释；
文本分类器的解释；
图像分类器的解释；
```
LIME被用作解释机器学习模型的解释，通过LIME我们可以知道为什么模型会这样进行预测。

本文我们就重点观测一下LIME是如何对预测结果进行解释的。

&nbsp;
## 对单个样本进行预测解释
下面的图中表明了单个样本的预测值中各个特征的贡献。
```
import lime
from lime import lime_tabular

explainer = lime_tabular.LimeTabularExplainer(
    training_data=np.array(X_train),
    feature_names=X_train.columns,
    class_names=['bad', 'good'],
    mode='classification'
)
```

&nbsp;
## 适用问题
LIME可以认为是SHARP的升级版，它通过预测结果解释机器学习模型很简单。它为我们提供了一个很好的方式来向非技术人员解释地下发生了什么。您不必担心数据可视化，因为LIME库会为您处理数据可视化。

&nbsp;
## References
[Github8.9K，目前最佳模型解释器-LIME！](https://mp.weixin.qq.com/s/47omhEeHqJdQTtciLIN2Hw)
