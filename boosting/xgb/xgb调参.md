## Xgboost的优势
XGBoost(eXtreme Gradient Boosting)是Gradient Boosting算法的一个优化的版本。  

XGBoost算法可以给预测模型带来能力的提升。当我对它的表现有更多了解的时候，当我对它的高准确率背后的原理有更多了解的时候，我发现它具有很多优势：  
> Regularization（正则化）  
Parallel Processing（并行处理）  
High Flexibility（高度灵活）-- Xgboost可以让使用者自定义优化目标与评估标准。   
Handling Missing Values（处理缺失值）  
Tree Pruning（树剪枝）-- Xgboost中有个参数max_depth，因此Xgboost会持续分裂直到达到max_depth，然后回溯剪枝  
Built-in Cross-Validation（内置的交叉验证）-- Xgboost允许用户在每次boosting迭代的过程中应用交叉验证  
Continue on Existing Model（继续现有模型）-- 用户可以从上一次运行的最后一次迭代中开始训练XGBoost模型。这在某些特定的应用程序中具有很大的优势。

注：讲解详见原论文

&nbsp;
## XGBoost的参数
XGBoost的作者把所有的参数分成了三类：   
> 1、通用参数：宏观函数控制。   
2、Booster参数：控制每一步的booster(tree/regression)。   
3、学习目标参数：控制训练目标的表现（指导优化任务的执行）

### 通用参数
> **1.booster [default=gbtree]**  
```
选择每次迭代过程中需要运行的模型，一共有两种选择：  
gbtree: tree-based models  
gbliner：线性模型
```
> **2.silent [default=0]** 
```
设置模型是否有logo打印：  
0：有打印  
1：无打印  
```
> **3.nthread [default to maximum number of threads available if not set]**  
```
这个主要用于并行处理的，如果不指定值，工具会自动检测  
```
> **剩余两个参数是Xgboost自动指定的，无需设置**

### 提升参数
虽然有两种类型的booster，但是我们这里只介绍tree。因为tree的性能比线性回归好得多，因此我们很少用线性回归。  

1.eta [default=0.3, alias: learning_rate]
```
学习率，可以缩减每一步的权重值，使得模型更加健壮：
典型值一般设置为：0.01-0.2
```
2.min_child_weight [default=1]
```
定义了一个子集的所有观察值的最小权重和。
这个可以用来减少过拟合，但是过高的值也会导致欠拟合，因此可以通过CV来调整min_child_weight。
```
3.max_depth [default=6]
```
树的最大深度，值越大，树越复杂。
这个可以用来控制过拟合，典型值是3-10。
```
4.gamma [default=0, alias: min_split_loss]
```
这个指定了一个结点被分割时，所需要的最小损失函数减小的大小。
这个值一般来说需要根据损失函数来调整。
```
5.max_delta_step(默认= 0)
```
这个参数通常并不需要。
```
6.subsample [default=1]
```
样本的采样率，如果设置成0.5，那么Xgboost会随机选择一般的样本作为训练集。
```
7.colsample_bytree [default=1]
```
构造每棵树时，列采样率（一般是特征采样率）。
```
8.colsample_bylevel [default=1]
```
每执行一次分裂，列采样率。这个一般很少用，6和7参数调节就足够了。
```
9.lambda [default=1, alias: reg_lambda]
```
L2正则化（与岭回归中的正则化类似：传送门）这个其实用的很少。
```
10.alpha [default=0, alias: reg_alpha]
```
L1正则化（与lasso回归中的正则化类似：传送门）这个主要是用在数据维度很高的情况下，可以提高运行速度。
```
11.scale_pos_weight, [default=1]
```
在类别高度不平衡的情况下，将参数设置大于0，可以加快收敛。
```
### 学习任务参数
这类参数主要用来明确学习任务和相应的学习目标的  

1.objective [default=reg:linear]
```
这个主要是指定学习目标的：而分类，还是多分类or回归
“reg:linear” –linear regression：回归
“binary:logistic”：二分类
“multi:softmax” ：多分类，这个需要指定类别个数
```
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

2.eval_metric [default according to objective]
```
*评估方法，主要用来验证数据，根据一个学习目标会默认分配一个评估指标
“rmse”:均方根误差（回归任务）
“error”:分类任务
“map”:Mean average precision（平均准确率，排名任务）
等等
```
> **eval_metric [ default according to objective ]**    
>> 校验数据所需要的评价指标，不同的目标函数将会有缺省的评价指标（rmse for regression, and error for classification, mean average precision for ranking）  
用户可以添加多种评价指标，对于Python用户要以list传递参数对给程序，而不是map参数list参数不会覆盖’eval_metric’  
The choices are listed below:  
***“rmse”: root mean square error 均方根误差***   
***“logloss”: negative log-likelihood 负对数似然函数值***   
……  

3.seed [default=0]
```
随机数种子，可以用来生成可复制性的结果，也可用来调参
```

> **base_score [ default=0.5 ]**    
>> the initial prediction score of all instances, global bias  



### 示例
详见参考文档
### 总结
**不要幻想仅仅通过参数调优或者换一个稍微更好的模型使得最终结果有巨大的飞跃**。要想最后的结果有巨大的提升，可以通过特征工程、模型集成来实现。



类比GBM,[GBM](https://www.analyticsvidhya.com/blog/2016/02/complete-guide-parameter-tuning-gradient-boosting-gbm-python/)  
各类参数详见参考文档  


&nbsp;
## Scikit-Learn API
**n_estimators (int)** – Number of gradient boosted trees. ***Equivalent to number of boosting rounds***.  
[Python API Reference](https://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.training)

&nbsp;
## reference
[Xgboost参数调优的完整指南及实战](https://blog.csdn.net/u010665216/article/details/78532619)  
[Python机器学习笔记：XgBoost算法](https://www.cnblogs.com/wj-1314/p/9402324.html).  
[XGBoost参数调优完全指南（附Python代码）](https://blog.csdn.net/u010657489/article/details/51952785)
