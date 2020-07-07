## 一、plot_importance方法
**plot_importance方法默认取‘weight’类型，即特征出现次数。需显示设置为‘gain’类型，即特征的平均增益更为准确（下方训练模型参数默认gain）**  
示例：   
```
xgb.plot_importance(gbm,max_num_features=10)

xgb.plot_importance(gbm,max_num_features=10,importance_type='gain')
```
### 1、xgboost.plot_importance
xgboost.plot_importance(booster, ax=None, height=0.2, xlim=None, ylim=None, title='Feature importance', xlabel='F score', ylabel='Features', fmap='', importance_type='weight', max_num_features=None, grid=True, show_values=True, **kwargs)

Parameters:
> **importance_type (str, default "weight")**  
–How the importance is calculated: either “weight”, “gain”, or “cover”
```
”weight” is the number of times a feature appears in a tree
”gain” is the average gain of splits which use the feature
”cover” is the average coverage of splits which use the feature where coverage is defined 
        as the number of samples affected by the split
```
> **max_num_features (int, default None)**  
– Maximum number of top features displayed on plot. ***If None, all features will be displayed***.

### 2、lightgbm.plot_importance
lightgbm.plot_importance(booster, ax=None, height=0.2, xlim=None, ylim=None, title='Feature importance', xlabel='Feature importance', ylabel='Features', importance_type='split', max_num_features=None, ignore_zero=True, figsize=None, dpi=None, grid=True, precision=3, **kwargs)

Parameters类似xgb

&nbsp;
## 二、feature_importances_属性
**模型的feature_importances_属性，默认取‘gain’类型**    
示例：  
```
importances=gbm.feature_importances_  
indices=np.argsort(importances)[::-1] #[::-1]倒序  
# print (indices)  
for f in range(20):  
    print("%d. feature %d (%f)" % (f + 1, indices[f], importances[indices[f]])) 
```
### 1、class xgboost.XGBClassifier(objective='binary:logistic', **kwargs)  
Parameters：
```
importance_type (string, default "gain") 
– The feature importance type for the feature_importances_ property: either “gain”, “weight”, “cover”, “total_gain” or “total_cover”.
```
即，**模型在训练时，默认使用‘gain’的重要性类型，特征重要性属性也就是‘gain’类型**
### 2、property feature_importances_
Feature importances property
```
Note:
Feature importance is defined only for tree boosters.
Feature importance is only defined when the decision tree model is chosen as base learner (booster=gbtree). 
It is not defined for other base learner types, such as linear learners (booster=gblinear).
```

&nbsp;
## reference
[Plotting API](https://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.plotting)  
[lightgbm.plot_importance](https://lightgbm.readthedocs.io/en/latest/pythonapi/lightgbm.plot_importance.html#lightgbm.plot_importance)   
[class xgboost.XGBClassifier](https://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.sklearn)
