## pd.cut
(x, bins, right=True, labels=None, retbins=False, precision=3, include_lowest=False)
```
x：需要离散化的数组、Series、DataFrame对象.The input array to be binned. Must be 1-dimensional.
bins：分组的依据
right：传入False则指定左边为闭端
```

&nbsp;
## qcut
跟cut()按照变量的值对变量进行分割不同， qcut()是按变量的数量来对变量进行分割，并且尽量保证每个分组里变量的个数相同。

&nbsp;
## reference
[Pandas详解二十二之离散化（分组、区间化）](https://blog.csdn.net/weixin_38168620/article/details/81189278)  
[pandas.cut](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.cut.html)
