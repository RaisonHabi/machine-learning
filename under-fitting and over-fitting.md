## 背景
The poor performance of our model maybe because, the model is too simple to describe the target, or may be model is too complex to express the target.   
Why this happened? And how to deal with it?

&nbsp;
## 模型表现
| 训练集表现 | 测试集表现 | 问题 |
| ------ | ------ | ------ |
| <期望值 | <期望值 | Underfitting |
| >期望值 | 接近或略逊于训练集 | 合适 |
| >期望值 | 远差于训练集 | Overfitting |

&nbsp;
## 如何解决欠拟合和过拟合问题
欠拟合问题，根本的原因是特征维度过少，导致拟合的函数无法满足训练集，误差较大。  
比如是识别一只狗狗的模型,可能二哈被提取的特征比较少，导致训练出来的模型不能很好地匹配，表现得很差，甚至二哈都无法识别    
**欠拟合问题可以通过增加特征维度来解决**。

过拟合问题，根本的原因则是特征维度过多，导致拟合的函数完美的经过训练集，但是对新数据的预测结果则较差。  
解决过拟合问题，则有2个途径：  
> 减少特征维度; 可以人工选择保留的特征，或者模型选择算法  
正则化; 保留所有的特征，通过降低参数θ的值，来影响模型

&nbsp;
## reference
[欠拟合（Underfitting） & 过拟合（Overfitting） & 正则化（Regularization）](https://blog.csdn.net/tz_zs/article/details/78588478)  
