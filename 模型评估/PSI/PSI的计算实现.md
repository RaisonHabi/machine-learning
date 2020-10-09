## 一、PSI计算 python实现
### 1.计算模型分的PSI
```
def psi_calc(actual,predict,bins=10):
    '''
    功能: 计算PSI值，并输出实际和预期占比分布曲线
    输入值:
    actual: 一维数组或series，代表训练集模型得分
    predict: 一维数组或series，代表测试集模型得分
    bins: 违约率段划分个数
    输出值:
    字典，键值关系为{'psi': PSI值，'psi_fig': 实际和预期占比分布曲线}
    '''
    psi_dict = {}
    actual = np.sort(actual)
    predict = np.sort(predict)
    actual_len = len(actual)
    predict_len = len(predict)
    psi_cut = []
    actual_bins = []
    predict_bins = []
    actual_min = actual.min()
    actual_max = actual.max()
    cuts = []
    binlen = (actual_max-actual_min) / bins
    for i in range(1, bins):
        cuts.append(actual_min+i*binlen)
    for i in range(1, (bins+1)):
        if i == 1:
            lowercut = float('-Inf')
            uppercut = cuts[i-1]
        elif i == bins:
            lowercut = cuts[i-2]
            uppercut = float('Inf')
        else:
            lowercut = cuts[i-2]
            uppercut = cuts[i-1]
        actual_cnt = ((actual >= lowercut) & (actual < uppercut)).sum()+1
        predict_cnt = ((predict >= lowercut) & (predict < uppercut)).sum()+1
        actual_pct = (actual_cnt+0.0) / actual_len
        predict_pct = (predict_cnt+0.0) / predict_len
        psi_cut.append((actual_pct-predict_pct) * math.log(actual_pct/predict_pct))
        actual_bins.append(actual_pct)
        predict_bins.append(predict_pct)
    psi = sum(psi_cut)
    nbins = len(actual_bins)
    xlab = np.arange(1, nbins+1)
    fig = plt.figure()
    plt.plot(xlab, np.array(actual_bins),'r',label='actual')
    plt.plot(xlab, np.array(predict_bins),'b',label='predict')
    plt.legend(loc='best')
    plt.title('Psi Curve')
    plt.close()
    psi_dict['psi'] = psi
    psi_dict['psi_fig'] = fig
    return psi_dict

```
上述计算代码中，我们对变量的分箱采用最简单的无监督的等距的分箱方法，这个在PSI最原始的定义中也是这么定义的。

后面我们会介绍多种分箱方法，都可以来代替本代码中的分箱代码，进而得到不同分箱下的PSI。
### 2.特征PSI计算 python实现
上述代码一般用于计算模型分的PSI，有一个前提假设，就是我们的变量是连续的，
如果我们要用来计算的PSI的特征离散且本身的离散值个数就小于分bin数，为了解决这样的情况，我们重写了上面的代码来计算不同类型特征的PSI，代码如下：
```
def fea_psi_calc(actual,predict,bins=10):
    '''
    功能: 计算连续变量和离散变量的PSI值
    输入值:
    actual: 一维数组或series，代表训练集中的变量
    predict: 一维数组或series，代表测试集中的变量
    bins: 违约率段划分个数
    输出值:
    字典，键值关系为{'psi': PSI值，'psi_fig': 实际和预期占比分布曲线}
    '''
    psi_dict = {}
    actual = np.sort(actual)
    actual_distinct = np.sort(list(set(actual)))
    predict = np.sort(predict)
    predict_distinct = np.sort(list(set(predict)))
    actual_len = len(actual)
    actual_distinct_len = len(actual_distinct)
    predict_len = len(predict)
    predict_distinct_len = len(predict_distinct)
    psi_cut = []
    actual_bins = []
    predict_bins = []
    actual_min = actual.min()
    actual_max = actual.max()
    cuts = []
    binlen = (actual_max-actual_min) / bins
    if (actual_distinct_len<bins):
        for i in actual_distinct:
            cuts.append(i)
        for i in range(2, (actual_distinct_len+1)):
            if i == bins:
                lowercut = cuts[i-2]
                uppercut = float('Inf')
            else:
                lowercut = cuts[i-2]
                uppercut = cuts[i-1]
            actual_cnt = ((actual >= lowercut) & (actual < uppercut)).sum()+1
            predict_cnt = ((predict >= lowercut) & (predict < uppercut)).sum()+1
            actual_pct = (actual_cnt+0.0) / actual_len
            predict_pct = (predict_cnt+0.0) / predict_len
            psi_cut.append((actual_pct-predict_pct) * math.log(actual_pct/predict_pct))
            actual_bins.append(actual_pct)
            predict_bins.append(predict_pct)
    else:
        for i in range(1, bins):
            cuts.append(actual_min+i*binlen)
        for i in range(1, (bins+1)):
            if i == 1:
                lowercut = float('-Inf')
                uppercut = cuts[i-1]
            elif i == bins:
                lowercut = cuts[i-2]
                uppercut = float('Inf')
            else:
                lowercut = cuts[i-2]
                uppercut = cuts[i-1]
            actual_cnt = ((actual >= lowercut) & (actual < uppercut)).sum()+1
            predict_cnt = ((predict >= lowercut) & (predict < uppercut)).sum()+1
            actual_pct = (actual_cnt+0.0) / actual_len
            predict_pct = (predict_cnt+0.0) / predict_len
            psi_cut.append((actual_pct-predict_pct) * math.log(actual_pct/predict_pct))
            actual_bins.append(actual_pct)
            predict_bins.append(predict_pct)
    psi = sum(psi_cut)
    nbins = len(actual_bins)
    xlab = np.arange(1, nbins+1)
    psi_dict['psi'] = psi
    return psi_dict

```
但是计算出的PSI值和我们平常默认的十等分计算的psi值略有差异，在确定阈值的时候根据经验适当调整（一般离散变量的分箱数越少，变量的psi越小，这个可以利用上述代码，改变bins大小进行试验。）

&nbsp;
## 二、PSI计算 hive实现
### 1.SQL实现PSI
上面介绍了python实现特征PSI的计算方法，实际中，在线上环境中，为了实现快速的上线迭代看效果，更方便的是武器无疑是SQL了，这里我们介绍用SQL实现PSI的计算。
```
 select
      feature,
      dt,
      sum(psi_bins) as psi
    from
      (
        select
          feature,
          bins,
          (last_rate - rate) * ln(last_rate / rate) as psi_bins,
          dt
        from
          (
            select
              feature,
              bins,
              rate,
              lead(rate, 1 , 0.000001) over(
                partition by feature,
                bins
                order by
                  dt desc
              ) as last_rate,
              dt
            from
              table name
          ) a
      ) b

```
这里有个前提，计算的特征已经进行过离散化了，一般特征的离散化可以用case when来解决；同时提前计算好，每个特征不同分箱内值所占的比例，这部分比较简单，就不介绍了，有疑问可以私信交流。下面主要介绍下用到的一个窗口函数lead

&nbsp;
## 三、kaggle实战
### 1. 导入数据
### 2. 部分字段处理
### 3. Y值处理
### 4. 坏账率分析
### 5. 部分特征处理
### 6. 训练测试集划分（根据时间）
### 7. 计算特征PSI
### 8. 根据PSI筛选特征
```
columns_select = [i for i in X_columns]
fea_psi_compare = pd.DataFrame(index=['df_train'],columns=columns_select)

for column in columns_select:
    psi = fea_psi_calc(df_train[column].dropna(),df_test[column].dropna())['psi']
    fea_psi_compare.loc['df_train',column] = psi
    print(column,psi)
fea_psi_compare_T = fea_psi_compare.T;

fea_psi_compare_T[fea_psi_compare_T['df_train']>1].sort_values(by='df_train',ascending=False).head()

psi_drop_columns = fea_psi_compare_T[fea_psi_compare_T['df_train']>0.25].sort_values(by='df_train',ascending=False).index.tolist()

print('psi_drop_columns:{}'.format(psi_drop_columns))

```
输出结果如上，可根据经验阈值筛选特征。 本次分享就到这里，后续分享方向：1. kaggle比赛优秀kernel分享 2.风控相关业务知识分享 3.风控技术细节分享，期待你得留言反馈哦~

&nbsp;
## reference
[使用hive和python多种方式实现PSI的计算](https://juejin.im/post/6854573210571603975)
