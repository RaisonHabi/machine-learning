## 在运行Xgboost之前，必须设置general parameters，booster parameters和task parameters：
> **通用参数（General Parameters）**：该参数控制在提升（boosting）过程中使用哪种booster，常用的booster有树模型（tree）和线性模型（linear model）  
**Booster参数（Booster Parameters）**：这取决于使用哪种booster  
**学习目标参数（Task Parameters）**：控制学习的场景，例如在回归问题中会使用不同的参数控制排序  

## 学习目标参数
> **objective [ default=reg:linear ]**     
>> 定义学习任务及相应的学习目标，可选的目标函数如下：  
“reg:linear” –线性回归。  
“reg:logistic” –逻辑回归。  
“binary:logistic” –二分类的逻辑回归问题，输出为概率。  
“binary:logitraw” –二分类的逻辑回归问题，输出的结果为wTx。  
“count:poisson” –计数问题的poisson回归，输出结果为poisson分布。  
在poisson回归中，max_delta_step的缺省值为0.7。(used to safeguard optimization)  
“multi:softmax” –让XGBoost采用softmax目标函数处理多分类问题，同时需要设置参数num_class（类别个数）  
“multi:softprob” –和softmax一样，但是输出的是ndata * nclass的向量，可以将该向量reshape成ndata行nclass列的矩阵。每行数据表示样本所属于每个类别的概率。  
“rank:pairwise” –set XGBoost to do ranking task by minimizing the pairwise loss

> **base_score [ default=0.5 ]**    
>> the initial prediction score of all instances, global bias  

> **eval_metric [ default according to objective ]**    
>> 校验数据所需要的评价指标，不同的目标函数将会有缺省的评价指标（rmse for regression, and error for classification, mean average precision for ranking）  
用户可以添加多种评价指标，对于Python用户要以list传递参数对给程序，而不是map参数list参数不会覆盖’eval_metric’  
The choices are listed below:  
***“rmse”: root mean square error 均方根误差***   
***“logloss”: negative log-likelihood 负对数似然函数值***   
……  

## reference
[Python机器学习笔记：XgBoost算法](https://www.cnblogs.com/wj-1314/p/9402324.html)
