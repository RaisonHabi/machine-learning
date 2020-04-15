## IV值进行特征选择

&nbsp;
##  随机森林进行特征选择
随机森林是以决策树为基学习器的集成学习算法。随机森林非常简单，易于实现，计算开销也很小，更令人惊奇的是它在分类和回归上表现出了十分惊人的性能，因此，随机森林也被誉为“代表集成学习技术水平的方法”。  

```
from sklearn.ensemble import RandomForestClassifier


# max_depth: 默认可以不输入，如果不输入的话，决策树在建立子树的时候不会限制子树的深度。
# 一般来说，数据少或者特征少的时候可以不管这个值。如果模型样本量多，特征也多的情况下，推荐限制这个最大深度，具体的取值取决于数据的分布
rf = RandomForestClassifier(max_depth=2)
rf.fit(x_train, y_train)

importances = rf.feature_importances_
indices = np.argsort(importances)[::-1]
# for f in range(21):  
    # print("%d. feature %d (%f)" % (f + 1, indices1[f], importances1[indices1[f]]))  

threshold=0.001
feas_num=865       # 特征总数
ori_fea_start=2    # 第一个特征的原始序号
index_lis=[]
for i in range(feas_num):
    if importances[indices[i]] > threshold:
        index_lis.append(indices[i] + ori_fea_start)
    
index_sort=sorted(index_lis)
# 筛选特征
x_selected = X_train.iloc[:,index_sort]
print(x_selected.shape)
```

随机森林是一种非常流行的特征选择方法，它易于使用，一般不需要feature engineering、调参等繁琐的步骤，并且很多工具包都提供了平均不纯度下降方法。  
它的两个主要问题，**1是重要的特征有可能得分很低（关联特征问题（共线特征？）），2是这种方法对特征变量类别多的特征越有利（偏向问题）**。  
尽管如此，这种方法仍然非常值得在你的应用中试一试

&nbsp;
## reference
[学习笔记（九）用IV值和随机森林挑选特征](https://blog.csdn.net/zhangyunpeng0922/article/details/84591046)  
[ML - 贷款用户逾期情况分析5 - 特征工程2（特征选择）](https://blog.csdn.net/a786150017/article/details/84573202)  
[机器学习中，有哪些特征选择的工程方法？ - 城东的回答 - 知乎](https://www.zhihu.com/question/28641663/answer/110165221)
