## 背景
使用诸如梯度提升之类的决策树方法的集合的好处是，它们可以从训练的预测模型自动提供特征重要性的估计。

## 梯度提升中的特征重要性
使用梯度提升的好处是，在构建增强树之后，检索每个属性的重要性分数是相对简单的。  

Generally, importance provides a score that indicates how useful or valuable each feature was in the construction of the boosted decision trees within the model.   
**The more an attribute is used to make key decisions with decision trees, the higher its relative importance.**  

***详见英文版，参考的翻译不一定准确！***    

对于数据集中的每个属性，明确计算此重要性，允许对属性进行排名并相互比较。

通过每个属性分割点改进表现度量的量来计算单个决策树的重要性，并由节点负责的观察数量加权。表现度量可以是用于选择分裂点的纯度（基尼指数）或另一个更具体的误差函数。

然后，在模型中的所有决策树中对要素重要性进行平均。


## reference
[在 Python 中使用 XGBoost 的特征重要性和特征选择](https://github.com/apachecn/ml-mastery-zh/blob/master/docs/xgboost/feature-importance-and-feature-selection-with-xgboost-in-python.md)  
[Feature Importance and Feature Selection With XGBoost in Python](https://machinelearningmastery.com/feature-importance-and-feature-selection-with-xgboost-in-python/)
