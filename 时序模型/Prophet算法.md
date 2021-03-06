## 一、Prophet 简介
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
## 二、Prophet 的算法原理
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
## 三、Prophet使用
### 预测区间
默认情况下， Prophet 的返回结果中会包括预测值 yhat 的预测区间。当然，预测区间的估计需建立在一些重要的假设前提下。

在预测时，不确定性主要来源于三个部分：趋势中的不确定性、季节效应估计中的不确定性和观测值的噪声影响。

预测中，不确定性最大的来源就在于未来趋势改变的不确定性。  
这种衡量不确定性的方法具有以下性质：变化速率灵活性更大时（通过增大参数 changepoint_prior_scale 的值），预测的不确定性也会随之增大。原因在于如果将历史数据中更多的变化速率加入了模型，也就代表我们认为未来也会变化得更多，就会使得预测区间成为反映过拟合的标志。

预测区间的宽度（默认下，是 80% ）可以通过设置 interval_width 参数来控制：

使用贝叶斯取样的方法来得到季节效应的不确定性，可通过设置 mcmc.samples 参数（默认下取 0 ）


&nbsp;
## 四、ps
### 4.1 若缺失中间日期，prophet会自动拟合补全（实验验证）
### 4.2 INFO:fbprophet:n_changepoints greater than number of observations. Using 22
In essence, the default number of changepoint is 25 uniformly across the data. If your data is less than that, it will result in a warning   
[Trend Changepoints](https://facebook.github.io/prophet/docs/trend_changepoints.html)
### 4.3 异常值
一些异常值破坏了季节效应的估计，因此未来的预测也会永久地受到这个影响。最好的解决方法就是移除这些异常值


&nbsp;
## 五、参数
### 适用范围
对于不具有明显趋势性、周期性的时间序列，使用Prophet进行预测就不适合了
### Prophet（）的主要参数
```
#设置跟随性： changepoint_prior_scale=0.05 值越大，拟合的跟随性越好，可能会过拟合

#设置置信区间：interval_width=0.8（默认值）,值越小，上下线的带宽越小。

#指定预测类型： growth='linear'或growth = "logistic" ，默认应该是linear。

#马尔科夫蒙特卡洛取样（MCMC）： mcmc_samples=0,会计算很慢。距离意义不清楚

#设置寻找突变点的比例：changepoint_range=0.9 默认从数据的前90%中寻找异常数据。
预测这个正弦曲线，如果不设置changepoint_range=1，预测的结果是不对的，不知道为什么。
```
### make_future_dataframe( ）的主要参数
```
#periods 周期，一般是根据实际意义确定，重点：后续预测的长度是一个周期的长度。

#freq 我见的有‘MS‘、H、M ，预测sin，要设置H ，个人理解数据如果变化很快，要用H
```
### 其他的内置参数
```
n_changepoints 是预设转折点数量

changepoint_range 是设定转折点可以存在的范围，.1表示存在于历史数据的前十分之一，.5表示在历史数据的前半部分，其余同理。

changepoint_prior_scale 是设置模型对转折点拟合的灵敏度，值越高越灵活。

changepoints=[] 是指定转折点的具体位置

yearly_seasonality 是年的拟合度，值越高越灵活，同时可以选择True和False来设定是否进行年度的拟合。
同理与weekly_seasonality和daily_seasonality。

holidays_prior_scale 是假期的拟合度，同样值越高越灵活，同时前提是你需要有假期信息的加入。

seasonality_mode=‘multiplicative’ 是模型学习的方式，默认情况下为加性的，如果如上所示来设置，则是乘性的(multiplicative)。
```

&nbsp;
## reference
[Facebook 时间序列预测算法 Prophet 的研究](https://zhuanlan.zhihu.com/p/52330017)   
[Quick Start](https://facebook.github.io/prophet/docs/quick_start.html#python-api)   
[时间序列模型 Prophet 参数设置 实例 源码](https://blog.csdn.net/u014710355/article/details/97265517)   
[Prophet 之使用篇（五）——预测区间](https://vectorf.github.io/2017/03/14/20170314-Prophet%E4%B9%8B%E4%BD%BF%E7%94%A8%E7%AF%87%EF%BC%88%E4%BA%94%EF%BC%89/)   
[Prophet 之使用篇（六）——异常值](https://vectorf.github.io/2017/03/14/20170314-Prophet%E4%B9%8B%E4%BD%BF%E7%94%A8%E7%AF%87%EF%BC%88%E5%85%AD%EF%BC%89/)  
[Prophet 之使用篇（七）——非日数据](https://vectorf.github.io/2017/03/14/20170314-Prophet%E4%B9%8B%E4%BD%BF%E7%94%A8%E7%AF%87%EF%BC%88%E4%B8%83%EF%BC%89/)  
