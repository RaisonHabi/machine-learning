## ks_2samp实现
直接调用stats.ks_2samp()函数来计算  
链接scipy.stats.ks_2samp¶为ks_2samp()实现源码，

&nbsp;
## crosstab实现
我们知道计算ks的核心就是好坏人的累积概率分布，我们采用pandas.crosstab函数来计算累积概率分布。

&nbsp;
## roc_curve实现
我们同时发现在sklearn库中的roc_curve函数计算roc和auc时，计算过程中已经得到好坏人的累积概率分布，同时我们利用sklearn.metrics.roc_curve来计算ks值

&nbsp;
## 代码详见链接，自己实现的ks
```
def compute_ks(data):  
    sorted_list = data.sort_values(['score'], ascending=[True])  
    total_bad=49606  
    total_good = sorted_list.shape[0] - total_bad    
    max_ks = 0.0  
    good_count = 0.0  
    bad_count = 0.0
    
    
    lena=sorted_list.shape[0]
    print(lena)
    tem_sort=sorted_list[:int(math.floor(lena*0.01))]
    tem_sort_l=tem_sort[(tem_sort['user_id'].isin(uid1))]
    bad_count+=tem_sort_l.shape[0]
    print(bad_count)
    good_count+=tem_sort.shape[0]-tem_sort_l.shape[0]
    print(good_count)
    val = abs(bad_count/total_bad - good_count/total_good)
    max_ks = max(max_ks, val)
    
    for (i,j) in zip(np.arange(0.01,0.99,0.01),np.arange(0.02,1.00,0.01)):
        index1=int(math.floor(lena*i))
        index2=int(math.floor(lena*j))
        #print(index1,index2)
        tem_sort=sorted_list[index1:index2]
        tem_sort_l=tem_sort[(tem_sort['user_id'].isin(uid1))]
        
        bad_count+=tem_sort_l.shape[0]
        good_count+=tem_sort.shape[0]-tem_sort_l.shape[0]
        val = abs(bad_count/total_bad - good_count/total_good)
        max_ks = max(max_ks, val)
    

    tem_sort=sorted_list[int(math.floor(lena*0.99)):]
    bad_count+=tem_sort_l.shape[0]
    good_count+=tem_sort.shape[0]-tem_sort_l.shape[0]
    print(bad_count,good_count)
    val = abs(bad_count/total_bad - good_count/total_good)
    max_ks = max(max_ks, val)
    
    return max_ks

t0=time()  
print ("KS:",compute_ks(df2a))  
print('cost time',time()-t0) 
```

&nbsp;
## ks参考代码1
```
def my_ks(y, pred, n = 1000):  
    data = {"y": np.array(y), "pred":np.array(pred)}  
    df = pd.DataFrame(data)  
    all_true = sum(y)  
    all_false = len(y) - all_true  
    #pre_percentile = np.unique(pred)  
    #o_pre = sorted(pre_percentile)  
    ks = 0.0  
    for i in np.arange(0.0, 1.0, 1.0 / n):  
        tp = sum((df.y == 1) & (df.pred >= i)) #o_pre[i])  
        tpr = tp * 1.0 / all_true
        fp = sum((df.y == 0) & (df.pred >= i)) #o_pre[i])  
        fpr = fp * 1.0 / all_false  
        if (tpr - fpr) > ks:  
            ks = tpr - fpr  
    return ks 
```
    
&nbsp;
## ks参考代码2
```
def eval_ks(y, score, pos_label=None):
    """
    :param y: series
    :param scores: series
    :param pos_label: int or str,set minority as positive by default
    :return:
    """

    if not isinstance(y, pd.Series):
        y = pd.Series(y)
    if not isinstance(score, pd.Series):
        score = pd.Series(score)

    mask_ = y.notna() & score.notna()

    y = y[mask_]
    score = score[mask_]

    y.mean = y.value_counts().index[-1]

    if pos_label is None:
        pos_label = y.value_counts().index[-1]

    fpr, tpr, thresholds = roc_curve(y, score, pos_label=pos_label)
    return max(abs(fpr - tpr))
```

&nbsp;
## refrence
[数据挖掘建模评价指标-KS指标的python代码实现的两种方法](https://www.jianshu.com/p/fec4105a60d7)  
[风控模型中的K-S理解以及python实现](https://zhuanlan.zhihu.com/p/42656285)
