## 一、模型
### 1.1 AR （Auto Regression）模型
自回归模型描述当前值与历史值之间的关系，用变量自身的历史时间数据对自身进行预测。

一般的P阶自回归模型 AR：
### 1.2 MA （Moving Average）模型
在AR模型中，如果 [公式] 不是一个白噪声，通常认为它是一个q阶的移动平均。
### 1.3 ARMA 模型
将AR（p）与MA（q）结合，得到一个一般的自回归移动平均模型ARMA（p，q）：
### 1.4 ARIMA 模型
将自回归模型（AR）、移动平均模型（MA）和差分法结合，我们就得到了差分自回归移动平均模型 ARIMA（p、d、q），其中 d 是需要对数据进行差分的阶数。

&nbsp;
## 二、ARIMA 实战
### 2.1 生成 ARIMA 模型的基本步骤：
（详见链接）
### 2.2 pmdarima库 auto_arima
```
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
# %matplotlib inline

import pmdarima as pm


auto = pm.auto_arima(y_train, d=n_diffs, seasonal=False, stepwise=True,
                     suppress_warnings=True, error_action="ignore", max_p=6,
                     max_order=None, trace=True)
                    
print(auto.order)
# (0, 1, 0)

fc, conf_int = model.predict(n_periods=1, return_conf_int=True)
```

[pmdarima.arima.auto_arima](https://alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html)

### 2.3 modl.predict(n_periods=5, return_conf_int=True)
predict(n_periods=10, X=None, return_conf_int=False, alpha=0.05, **kwargs)
```
Forecast future values

Generate predictions (forecasts) n_periods in the future. 
Note that if exogenous variables were used in the model fit, 
they will be expected for the predict procedure and will fail otherwise.
```
```
n_periods : int, optional (default=10)
The number of periods in the future to forecast.

X : array-like, shape=[n_obs, n_vars], optional (default=None)
An optional 2-d array of exogenous variables. If provided, 
these variables are used as additional features in the regression operation. 
This should not include a constant or trend. 
Note that if an ARIMA is fit on exogenous features, it must be provided exogenous features for making predictions.

return_conf_int : bool, optional (default=False)
Whether to get the confidence intervals of the forecasts.

alpha : float, optional (default=0.05)
The confidence intervals for the forecasts are (1 - alpha) %
```

[predict](https://alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.ARIMA.html#pmdarima.arima.ARIMA.predict)


&nbsp;
## reference
[时间序列分析（1） 基本概念与实战](https://zhuanlan.zhihu.com/p/60023855)  
[时间序列分析（2） ARIMA 模型](https://zhuanlan.zhihu.com/p/60648709)  
[6.6 STL 分解法](https://otexts.com/fppcn/stl.html)   
[8.7 ARIMA modelling in R](https://otexts.com/fpp2/arima-r.html)   
[Stock market prediction](https://alkaline-ml.com/pmdarima/usecases/stocks.html)   
[user_guide](https://alkaline-ml.com/pmdarima/user_guide.html)
