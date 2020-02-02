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
## XGBoost的参数
XGBoost的作者把所有的参数分成了三类：   
> 1、通用参数：宏观函数控制。   
2、Booster参数：控制每一步的booster(tree/regression)。   
3、学习目标参数：控制训练目标的表现。 

类比GBM,[GBM](https://www.analyticsvidhya.com/blog/2016/02/complete-guide-parameter-tuning-gradient-boosting-gbm-python/)  
各类参数详见参考文档  

## reference
[Xgboost参数调优的完整指南及实战](https://blog.csdn.net/u010665216/article/details/78532619)  
[XGBoost参数调优完全指南（附Python代码）](https://blog.csdn.net/u010657489/article/details/51952785)
