## Prophet 简介
Github：https://github.com/facebook/prophet   
官方网址：https://facebook.github.io/prophet/   
论文名字与网址：Forecasting at scale，https://peerj.com/preprints/3190/

从官网的介绍来看，Facebook 所提供的 prophet 算法不仅可以处理时间序列存在一些异常值的情况，也可以处理部分缺失值的情形，还能够几乎全自动地预测时间序列未来的走势。  
从论文上的描述来看，这个 prophet 算法是基于时间序列分解和机器学习的拟合来做的，其中在拟合模型的时候使用了 pyStan 这个开源工具，因此能够在较快的时间内得到需要预测的结果。  
除此之外，为了方便统计学家，机器学习从业者等人群的使用，prophet 同时提供了 R 语言和 Python 语言的接口。   
从整体的介绍来看，如果是一般的商业分析或者数据分析的需求，都可以尝试使用这个开源算法来预测未来时间序列的走势。

对于商业分析等领域的时间序列，Prophet 可以进行很好的拟合和预测，**但是对于一些周期性或者趋势性不是很强的时间序列，用 Prophet 可能就不合适了**。  
但是，Prophet 提供了一种时序预测的方法，在用户不是很懂时间序列的前提下都可以使用这个工具得到一个能接受的结果。**具体是否用 Prophet 则需要根据具体的时间序列来确定。**

&nbsp;
## Prophet 的算法原理
### Prophet 数据的输入和输出
prophet 所做的事情就是：
```
输入已知的时间序列的时间戳和相应的值；
输入需要预测的时间序列的长度；
输出未来的时间序列走势。
输出结果可以提供必要的统计指标，包括拟合曲线，上界和下界等。
```
根据官网的描述，只要用 csv 文件存储两列即可，第一列的名字是 'ds', 第二列的名称是 'y'。第一列表示时间序列的时间戳，第二列表示时间序列的取值。  
通过 prophet 的计算，可以计算出 yhat，yhat_lower，yhat_upper，分别表示时间序列的预测值，预测值的下界，预测值的上界。
### Prophet 的算法实现
#### 趋势项模型 [公式]
#### 季节性趋势
#### 节假日效应（holidays and events）

&nbsp;
## reference
[Facebook 时间序列预测算法 Prophet 的研究](https://zhuanlan.zhihu.com/p/52330017)   
[Quick Start](https://facebook.github.io/prophet/docs/quick_start.html#python-api)
