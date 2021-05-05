## StatsModels
### Python StatsModels线性回归 (Python StatsModels Linear Regression)
```
# Load modules and data
import numpy as np
import statsmodels.api as sm
spector_data = sm.datasets.spector.load()
spector_data.exog = sm.add_constant(spector_data.exog, prepend=False)
# Fit and summarize OLS model
mod = sm.OLS(spector_data.endog, spector_data.exog)
res = mod.fit()
print(res.summary())
```
### 广义线性模型（GLM） (Generalized linear models (GLMs))
这些目前支持使用一参数指数族进行估计
```
# Load modules and data
import statsmodels.api as sm
data = sm.datasets.scotland.load()
data.exog = sm.add_constant(data.exog)
# Instantiate a gamma family model with the default link function.
gamma_model = sm.GLM(data.endog, data.exog, family=sm.families.Gamma())
gamma_results = gamma_model.fit()
print(gamma_results.summary())
```
### 广义估计方程（GEE） (Generalized Estimating Equations (GEEs))
从名称上可以清楚地看出，当观测值可能在一个聚类内而不是整个聚类之间相关时，GEE是用于面板，聚类或重复测量数据的广义线性模型。
```
# Load modules and data
import statsmodels.api as sm
import statsmodels.formula.api as smf
data = sm.datasets.get_rdataset('epil', package='MASS').data
fam = sm.families.Poisson()
ind = sm.cov_struct.Exchangeable()
# Instantiate model with the default link function.
mod = smf.gee("y ~ age + trt + base", "subject", data,cov_struct=ind, family=fam)
res = mod.fit()
print(res.summary())
```
### 稳健的线性模型 (Robust Linear Models)
Let’s create a more robust linear model. You must have observed it so far how easy it is to make such models with statsmodels:

让我们创建一个更健壮的线性模型。 到目前为止，您一定已经观察到了使用statsmodels创建此类模型有多么容易：
```
# Load modules and data
import statsmodels.api as sm
data = sm.datasets.stackloss.load()
data.exog = sm.add_constant(data.exog)
# Fit model and print summary
rlm_model = sm.RLM(data.endog, data.exog, M=sm.robust.norms.HuberT())
rlm_results = rlm_model.fit()
print(rlm_results.params)
```
### 线性混合效应模型 (Linear Mixed Effects Models)
Sometimes we have to work with dependent data. Such data is common to find when working with longitudinal and other study designs where multiple study designs are made. To analyse such data with regression Linear Mixed Effects models are very helpful:

有时我们必须处理依赖数据。 当使用纵向和其他研究设计进行多个研究设计时，通常会找到此类数据。 使用回归线性混合效应模型分析此类数据非常有帮助：
```
# Load modules and data
import statsmodels.api as sm
import statsmodels.formula.api as smf
# Fit model and print summary
data = sm.datasets.get_rdataset("dietox", "geepack").data
md = smf.mixedlm("Weight ~ Time", data, groups=data["Pig"])
mdf = md.fit()
print(mdf.summary())
```

&nbsp;
## reference
[Python StatsModels统计](https://blog.csdn.net/cunchi4221/article/details/107479053)
