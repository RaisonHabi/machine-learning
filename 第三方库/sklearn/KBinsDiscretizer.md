## sklearn.preprocessing.KBinsDiscretizer
Bin continuous data into intervals.

### Parameters
```
n_bins : int or array-like, shape (n_features,) (default=5)
  The number of bins to produce. The intervals for the bins are determined by the minimum and maximum of the input data.
  Raises ValueError if n_bins < 2.
  If n_bins is an array, and there is an ignored feature at index i, n_bins[i] will be ignored.

encode : {‘onehot’, ‘onehot-dense’, ‘ordinal’}, (default=’onehot’)
  Method used to encode the transformed result.
  onehot
    Encode the transformed result with one-hot encoding and return a sparse matrix. 
    Ignored features are always stacked to the right.
  onehot-dense
    Encode the transformed result with one-hot encoding and return a dense array. 
    Ignored features are always stacked to the right.
  ordinal
    Return the bin identifier encoded as an integer value.

strategy : {‘uniform’, ‘quantile’, ‘kmeans’}, (default=’quantile’)
  Strategy used to define the widths of the bins.
  uniform
    All bins in each feature have identical widths.
  quantile
    All bins in each feature have the same number of points.
  kmeans
    Values in each bin have the same nearest center of a 1D k-means cluster.
```
指定不同的分箱策略：KBinsDiscretizer类实现了不同的 binning策略，可以通过参数strategy进行选择。    
‘uniform’ 策略使用固定宽度的bins。 ‘quantile’ 策略在每个特征上使用分位数(quantiles)值以便具有相同填充的bins。 ‘kmeans’ 策略基于在每个特征上独立执行的k-means聚类过程定义bins。

&nbsp;
## sklearn.preprocessing.OneHotEncoder
Encode categorical features as a one-hot numeric array.
### Parameters
```
categories:‘auto’ or a list of array-like, default=’auto’
  Categories (unique values) per feature:

drop:{‘first’, ‘if_binary’} or a array-like of shape (n_features,), default=None

sparse:bool, default=True
  Will return sparse matrix if set True else will return an array.
...
```
### 示例
```
from sklearn.preprocessing import  OneHotEncoder

enc = OneHotEncoder()
enc.fit([[0, 0, 3],
         [1, 1, 0],
         [0, 2, 1],
         [1, 0, 2]])

# 如果不加 toarray() 的话，输出的是稀疏的存储格式，即索引加值的形式，也可以通过参数指定 sparse = False 来达到同样的效果
ans = enc.transform([[0, 1, 3]]).toarray()  
print(ans) 

# 输出 
## sparse
  (0, 0)	1.0
  (0, 3)	1.0
  (0, 8)	1.0

## array
[[ 1.  0.  0.  1.  0.  0.  0.  0.  1.]]
```
### todense 
应该是恢复密集表示，即恢复压缩前的矩阵的表示形式，而非转化为密集矩阵（因为原矩阵可能是稀疏矩阵，
也可能是密集矩阵，稀疏还是密集的定义是0元素个数占总元素个数的比例）

[sparse matrix、sparse vector、dense matrix等概念](https://github.com/RaisonHabi/machine-learning/blob/master/%E7%89%B9%E5%BE%81%E5%B7%A5%E7%A8%8B/%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86/sparse%20matrix%E3%80%81sparse%20vector%E3%80%81dense%20matrix%E7%AD%89%E6%A6%82%E5%BF%B5.md)

&nbsp;
## reference
[sklearn.preprocessing.KBinsDiscretizer](https://www.studyai.cn/modules/generated/sklearn.preprocessing.KBinsDiscretizer.html#sklearn.preprocessing.KBinsDiscretizer)

[sklearn.preprocessing.OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html)
