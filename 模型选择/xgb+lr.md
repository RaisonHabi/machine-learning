### 为什么Xgboost+LR的融合效果没有想象中那么好
1.当xgb模型本来拟合就比较好之后，xgb+lr 效果不一定会更好

2.树模型+LR的方法最适用于高纬稀疏数据(例如ctr预估，但风控评分卡不一定适合) 。树模型对这样的稀疏数据容易导致过拟合 
结合树模型的特征组合能力和LR的正则项来处理高维稀疏数据，既可以提高模型的拟合能力，又可以防止过拟合。
 
&nbsp;
## 原理
为什么要使用LR模型进行融合呢？这是因为LR (逻辑回归) 算法简单有效，成为工业界最常用的算法之一。  
但 LR 算法是线性模型，不能捕捉到非线性信息，需要大量特征工程找到特征组合。  
为了发现有效的特征组合，Facebook 在 2014年的论文ractical Lessons from Predicting Clicks on Ads at Facebook介绍了通过 GBDT （Gradient Boost Decision Tree）+ LR 的方案 （XGBoost 是 GBDT 的后续发展）。  
在这篇论文中他们提出了一种将GBDT作为feature transform的方法。随后在多个Kaggle 竞赛实践中，均证明了此思路的有效性。

### GBDT构造组合特征的方式
利用GBDT进行特征构造依据其模型组合方式一共有两种方式：
```
GBDT + LR
与原论文的实现方式一样，利用GBDT构造组合特征，再将组合特征进行one-hot编码（本实践代码也属此类）；

GBDT + FFM 或者 GBDT + 树模型
此时，使用利用GBDT构造的组合特征不再进行one-hot编码，而是直接利用输出叶节点的索引信息，
如果将GBDT组合特征输出到其他树模型，则可直接利用节点索引信息；若是将GBDT信息输出到FFM中，
依旧是利用索引信息，但是需要将索引信息组织成FFM数据输入形式。
```

&nbsp;
## 注意
[模型融合GBDT(xgb/lgbm/rf)+LR 的原理及实践](https://blog.csdn.net/zwqjoy/article/details/86612416)

rf_enc.transform(rf.apply(X_train_lr))按照rf.apply(X_train)的编码方式对rf.apply(X_train_lr)进行了onehot编码。  
这里需要注意得是：我们并没有像常规方式那样对rf.apply(X_train)和rf.apply(X_train_lr)先进行合并再进行onehot，这是因为在训练完成之后随机森林的树模型已经固定，即叶子节点的架构已经确定。   
因此rf.apply(X_train_lr)的值的范围和rf.apply(X_train)的值的范围必然一样！    
rf_lm.fit(rf_enc.transform(rf.apply(X_train_lr)), y_train_lr)使用随机森林构造的特征来训练LR
    
&nbsp;
## reference
[模型融合GBDT(xgb/lgbm/rf)+LR 的原理及实践](https://blog.csdn.net/zwqjoy/article/details/86612416).   
[XGBoost+LR融合的原理和简单实现](https://zhuanlan.zhihu.com/p/42123341).  
[特征组合之 XGBoost + LR](https://www.cnblogs.com/Ming-H/p/10897948.html). 
