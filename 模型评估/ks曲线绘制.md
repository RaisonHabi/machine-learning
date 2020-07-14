## 参考代码

### 版本一：
```
def PlotKS(preds, labels, n, asc):
    
    # preds is score: asc=1
    # preds is prob: asc=0
    
    pred = preds  
    bad = labels  
    ksds = DataFrame({'bad': bad, 'pred': pred})
    ksds['good'] = 1 - ksds.bad
    
    if asc == 1:
        ksds1 = ksds.sort_values(by=['pred', 'bad'], ascending=[True, True])
    elif asc == 0:
        ksds1 = ksds.sort_values(by=['pred', 'bad'], ascending=[False, True])
    ksds1.index = range(len(ksds1.pred))
    ksds1['cumsum_good1'] = 1.0*ksds1.good.cumsum()/sum(ksds1.good)
    ksds1['cumsum_bad1'] = 1.0*ksds1.bad.cumsum()/sum(ksds1.bad)
    
    if asc == 1:
        ksds2 = ksds.sort_values(by=['pred', 'bad'], ascending=[True, False])
    elif asc == 0:
        ksds2 = ksds.sort_values(by=['pred', 'bad'], ascending=[False, False])
    ksds2.index = range(len(ksds2.pred))
    ksds2['cumsum_good2'] = 1.0*ksds2.good.cumsum()/sum(ksds2.good)
    ksds2['cumsum_bad2'] = 1.0*ksds2.bad.cumsum()/sum(ksds2.bad)
    
    # ksds1 ksds2 -> average
    ksds = ksds1[['cumsum_good1', 'cumsum_bad1']]
    ksds['cumsum_good2'] = ksds2['cumsum_good2']
    ksds['cumsum_bad2'] = ksds2['cumsum_bad2']
    ksds['cumsum_good'] = (ksds['cumsum_good1'] + ksds['cumsum_good2'])/2
    ksds['cumsum_bad'] = (ksds['cumsum_bad1'] + ksds['cumsum_bad2'])/2
    
    # ks
    ksds['ks'] = ksds['cumsum_bad'] - ksds['cumsum_good']
    ksds['tile0'] = range(1, len(ksds.ks) + 1)
    ksds['tile'] = 1.0*ksds['tile0']/len(ksds['tile0'])
    
    qe = list(np.arange(0, 1, 1.0/n))
    qe.append(1)
    qe = qe[1:]
    
    ks_index = Series(ksds.index)
    ks_index = ks_index.quantile(q = qe)
    ks_index = np.ceil(ks_index).astype(int)
    ks_index = list(ks_index)
    
    ksds = ksds.loc[ks_index]
    ksds = ksds[['tile', 'cumsum_good', 'cumsum_bad', 'ks']]
    ksds0 = np.array([[0, 0, 0, 0]])
    ksds = np.concatenate([ksds0, ksds], axis=0)
    ksds = DataFrame(ksds, columns=['tile', 'cumsum_good', 'cumsum_bad', 'ks'])
    
    ks_value = ksds.ks.max()
    ks_pop = ksds.tile[ksds.ks.idxmax()]
    print ('ks_value is ' + str(np.round(ks_value, 4)) + ' at pop = ' + str(np.round(ks_pop, 4)))
    
    # chart
    plt.plot(ksds.tile, ksds.cumsum_good, label='cum_good',
                         color='blue', linestyle='-', linewidth=2)
                         
    plt.plot(ksds.tile, ksds.cumsum_bad, label='cum_bad',
                        color='red', linestyle='-', linewidth=2)
                        
    plt.plot(ksds.tile, ksds.ks, label='ks',
                   color='green', linestyle='-', linewidth=2)
                       
    plt.axvline(ks_pop, color='gray', linestyle='--')
    plt.axhline(ks_value, color='green', linestyle='--')
    plt.axhline(ksds.loc[ksds.ks.idxmax(), 'cumsum_good'], color='blue', linestyle='--')
    plt.axhline(ksds.loc[ksds.ks.idxmax(),'cumsum_bad'], color='red', linestyle='--')
    plt.title('KS=%s ' %np.round(ks_value, 4) +  
                'at Pop=%s' %np.round(ks_pop, 4), fontsize=15)
    
    
    return ksds
```
示例：
```
from pandas import DataFrame, Series

# y_pred = gbm.predict(X1_train)######注意sklearn接口的api训练的模型，predict输出的是标签！！！

y_pred_proba = gbm.predict_proba(X1_train)[:,1]

PlotKS(y_pred_proba, y1_train, 1000, 0)
```
[predict](https://xgboost.readthedocs.io/en/latest/python/python_api.html#xgboost.XGBClassifier.predict)  
[predict_proba](https://xgboost.readthedocs.io/en/latest/python/python_api.html#xgboost.XGBClassifier.predict_proba)

### 版本二：ks有问题！仅作参考
```
def plot_roc_ks_curve(true, pred):  
    fpr, tpr, thresholds= metrics.roc_curve(true, pred) #信用评分里面1是好人（白样本）
    plt.figure()
    plt.plot(fpr, tpr, color ='b', label='auc={:.4f}'.format(metrics.roc_auc_score(true, pred)))
    plt.plot(fpr, fpr, color = 'g')
    plt.legend()
    plt.title('ROC Curve', fontsize=15)
    plt.show()
    
    mat = pd.DataFrame({'thresholds':thresholds,'TPR':tpr,'FPR':fpr})
    ks_value = max(abs(fpr-tpr))
    x_0 = []
    for i in range(0,len(fpr)):
        x_0.append(i/len(fpr))
    plt.figure()
    plt.plot(x_0, fpr, label='cum_good_rate')
    plt.plot(x_0, tpr, label='cum_bad_rate') #cum_label=1_rate这里label=1为好人，但是还是叫bad_rate
    plt.plot(x_0, abs(fpr-tpr), label='diff')
#     # 标记ks
    x = np.argwhere(abs(fpr-tpr) == ks_value)[0, 0]/len(fpr)
    print(x, ks_value)
    plt.plot((x, x), (0, ks_value), label='x = {:.2f}, ks - {:.3f}'.format(x,ks_value), color='r', marker='o', markerfacecolor='r', markersize=5)
    plt.scatter((x, x), (0, ks_value), color='r')
    plt.legend()
    plt.title('KS Curve', fontsize=15)
    plt.show()
```

示例：
```
plot_roc_ks_curve(train_y, train_lgb)
plot_roc_ks_curve(valid_y, valid_lgb)
```
