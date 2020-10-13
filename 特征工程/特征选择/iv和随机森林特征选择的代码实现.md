## IV值进行特征选择
```
def iv_woe(data:pd.DataFrame, target:str, bins:int) -> (pd.DataFrame, pd.DataFrame):
    """计算woe和IV值
    
    参数：
    - data: dataframe数据
    - target: y列的名称
    - bins: 分箱数（默认是10）
    """
    newDF,woeDF = pd.DataFrame(), pd.DataFrame()
    cols = data.columns
    for ivars in cols[~cols.isin([target])]:
        # 数据类型在bifc中、且数据>10则分箱
#         ##bifc 是 b bool, i int, f float, c complex
#         if (data[ivars].dtype.kind in 'bifc') and (len(np.unique(data[ivars]))>10):

##等频分箱
#         if len(np.unique(data[ivars]))>bins:
#             binned_x = pd.qcut(data[ivars], bins,  duplicates='drop')
#             d0 = pd.DataFrame({'x': binned_x, 'y': data[target]})
#         else:
#             d0 = pd.DataFrame({'x': data[ivars], 'y': data[target]})

## 等宽
        binned_x = pd.cut(data[ivars], bins,  duplicates='drop')
        d0 = pd.DataFrame({'x': binned_x, 'y': data[target]})
    
        
        d = d0.groupby("x", as_index=False).agg({"y": ["count", "sum"]})
        d.columns = ['Cutoff', 'N', 'Events']
        d['% of Events'] = np.maximum(d['Events'], 0.5) / d['Events'].sum()
        d['Non-Events'] = d['N'] - d['Events']
        d['% of Non-Events'] = np.maximum(d['Non-Events'], 0.5) / d['Non-Events'].sum()
        d['WoE'] = np.log(d['% of Events']/d['% of Non-Events'])
        d['IV'] = d['WoE'] * (d['% of Events'] - d['% of Non-Events'])
        d.insert(loc=0, column='Variable', value=ivars)
#         print("Information value of " + ivars + " is " + str(round(d['IV'].sum(),6)))
        temp =pd.DataFrame({"Variable" : [ivars], "IV" : [d['IV'].sum()]}, columns = ["Variable", "IV"])
        newDF=pd.concat([newDF,temp], axis=0)
        woeDF=pd.concat([woeDF,d], axis=0)
    return newDF, woeDF
```


&nbsp;
##  随机森林进行特征选择
随机森林是以决策树为基学习器的集成学习算法。随机森林非常简单，易于实现，计算开销也很小，更令人惊奇的是它在分类和回归上表现出了十分惊人的性能，因此，随机森林也被誉为“代表集成学习技术水平的方法”。  

### 平均不纯度减少 mean decrease impurity
决策树中的每一个节点都是关于某个特征的条件，为的是将数据集按照不同的响应变量一分为二。  
利用不纯度可以确定节点（最优条件），对于**分类问题，通常采用 基尼不纯度 或者 信息增益** ，对于**回归问题，通常采用的是 方差 或者最小二乘拟合**。  
当训练决策树的时候，可以计算出每个特征减少了多少树的不纯度。对于一个决策树森林来说，可以算出每个特征平均减少了多少不纯度，并把它平均减少的不纯度作为特征选择的值。

下边的例子是sklearn中基于随机森林的特征重要度度量方法：

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

&nbsp;
### 平均精确率减少 Mean decrease accuracy
另一种常用的特征选择方法就是直接度量每个特征对模型精确率的影响。  
主要思路是**打乱每个特征的特征值顺序，并且度量顺序变动对模型的精确率的影响**。  
很明显，对于不重要的变量来说，打乱顺序对模型的精确率影响不会太大，但是对于重要的变量来说，打乱顺序就会降低模型的精确率。

这个方法sklearn中没有直接提供，但是很容易实现，下面继续在波士顿房价数据集上进行实现。详见[几种常用的特征选择方法](https://blog.csdn.net/LY_ysys629/article/details/53641569)


### 小结
随机森林是一种非常流行的特征选择方法，它易于使用，一般不需要feature engineering、调参等繁琐的步骤，并且很多工具包都提供了平均不纯度下降方法。  
它的两个主要问题，**1是重要的特征有可能得分很低（关联特征问题（共线特征？）），2是这种方法对特征变量类别多的特征越有利（偏向问题）**。  
尽管如此，这种方法仍然非常值得在你的应用中试一试

&nbsp;
## reference
[学习笔记（九）用IV值和随机森林挑选特征](https://blog.csdn.net/zhangyunpeng0922/article/details/84591046)  
[ML - 贷款用户逾期情况分析5 - 特征工程2（特征选择）](https://blog.csdn.net/a786150017/article/details/84573202)  
[机器学习中，有哪些特征选择的工程方法？ - 城东的回答 - 知乎](https://www.zhihu.com/question/28641663/answer/110165221)
