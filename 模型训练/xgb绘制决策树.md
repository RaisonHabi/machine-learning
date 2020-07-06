## 画XGBoost里面的决策树(decision tree)
XGBoost（eXtreme Gradient Boosting）全名叫极端梯度提升。  
[从原理上认识XGBoost](https://zhuanlan.zhihu.com/p/74153059)
```
XGBoost大体思路是串行的生成一系列CART回归树，每棵回归树都是去拟合上一颗树与目标值的残差，
这样重复进行，直到达到事先设定的树的个数或者收敛。
（残差：残差在数理统计中是指实际观察值与估计值（拟合值）之间的差。 
“残差”蕴含了有关模型基本假设的重要信息。 如果回归模型正确的话， 我们可以将残差看作误差的观测值。 
它应符合模型的假设条件，且具有误差的一些性质）

在第t步时，我们添加了一棵最优的CART树f_t，这棵最优的CART树f_t是怎么得来的呢？
非常简单，就是在现有的t-1棵树的基础上，使得目标函数最小的那棵CART树

从第0棵树开始，每一轮学习一棵新的树，再用上一棵树的结果与目标值比较构建新的树，这样串行生成。 
```
最近用XGBoost很多, 训练完模型后, 一般只是看看特征重要性(feature importance score). 
我对这种黑箱模型一般是不放心的, 所以喜欢把结果尽可能的画出来看看. 

**对比前几棵树的节点、重要性前几的特征，是否差别较大**。  
**另外，决策树可以排查特征是否重复出现在节点中，即不稳定特征？**
### 0.plot_importance
[Plotting API](https://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.plotting)
```
xgboost.plot_importance(booster, ax=None, height=0.2, xlim=None, ylim=None,
title='Feature importance', xlabel='F score', ylabel='Features', fmap='', importance_type='weight', 
max_num_features=None, grid=True, show_values=True, **kwargs)

max_num_features (int, default None) 
– Maximum number of top features displayed on plot. If None, all features will be displayed.
```
示例：
```
xgb.plot_importance(gbm,max_num_features=10)
```
### 1.安装graphviz包
pip install graphviz 安装后,执行后续画图方法报错！
```
RuntimeError: failed to execute ['dot', '-Tpdf', '-O', 'test'], make sure the Graphviz executables are on your systems' path
```
**解决方法：**
```
换为conda安装即可：Conda install graphviz

原因：The graphviz executable sit on a different path from your conda directory, if you use pip install graphviz.
```
### 2.方法一：to_graphviz

```
to_graphviz(booster, fmap='', num_trees=0, rankdir=None, 
yes_color=None, no_color=None, condition_node_params=None, leaf_node_params=None, **kwargs)
Convert specified tree to graphviz instance. 
IPython can automatically plot the returned graphiz instance. 
Otherwise, you should call .render() method of the returned graphiz instance.

num_trees (int, default 0) – Specify the ordinal number of target tree
rankdir (str, default "UT") – Passed to graphiz via graph_attr.'LR'从左到右
```
示例：
```
xgb.to_graphviz(gbm)
```
### 3.方法二：plot_tree
```
plot_tree(booster, fmap='', num_trees=0, rankdir=None, ax=None, **kwargs)

num_trees (int, default 0)
– Specify the ordinal number of target tree

rankdir (str, default "TB") 
– Passed to graphiz via graph_attr
```
示例：
```
xgb.plot_tree(gbm)
plt.show()
```

&nbsp;
## reference
[ Make sure the Graphviz executables are on your system's path](https://stackoverflow.com/questions/35064304/runtimeerror-make-sure-the-graphviz-executables-are-on-your-systems-path-aft)

[如何画XGBoost里面的决策树(decision tree)](https://zhuanlan.zhihu.com/p/32943164)
