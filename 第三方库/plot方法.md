## 1.密度分布
```
import seaborn as sns

plt.figure(figsize=(15, 10)) ##设置画布大小
sns.kdeplot(df_3_new[13])
sns.kdeplot(df_4_new[13])
sns.kdeplot(df_5_new[13])
sns.kdeplot(df_6_new[13])
sns.kdeplot(df_7_new[13])
plt.show()
```

&nbsp;
## 2.histogram直方图（注意bins取值）
### 2.1 纯Python实现histogram
```
>>> from collections import Counter

>>> recounted = Counter(a)
>>> recounted
Counter({0: 1, 1: 3, 3: 1, 2: 1, 7: 2, 23: 1})
```
总结：纯python实现频数表（非标准直方图），可直接使用collection.Counter方法实现。
### 2.2 使用Numpy实现histogram
np.histogram() 的内部进行解剖，看看到底是如何实现的（以最前面提到的a列表为例）。
```
>>> # 取a的最小值和最大值
>>> first_edge, last_edge = a.min(), a.max()

>>> n_equal_bins = 10  # NumPy得默认设置，10个分箱
>>> bin_edges = np.linspace(start=first_edge, stop=last_edge,
...                         num=n_equal_bins + 1, endpoint=True)
...
>>> bin_edges
array([ 0. ,  2.3,  4.6,  6.9,  9.2, 11.5, 13.8, 16.1, 18.4, 20.7, 23. ])
```
解释一下：首先获取a列表的最小值和最大值，然后设置默认的分箱数量，最后使用Numpy的 linspace 方法进行数据段分割。   
分箱区间的结果也正好与实际吻合，0到23均等分为10份，23/10，那么每份宽度为2.3。

除了np.histogram之外，还存在其它两种可以达到同样功能的方法：np.bincount() 和 np.searchsorted()，下面看看代码以及比较结果。
```
>>> bcounts = np.bincount(a)
>>> hist, _ = np.histogram(a, range=(0, a.max()), bins=a.max() + 1)

>>> np.array_equal(hist, bcounts)
True

>>> # Reproducing `collections.Counter`
>>> dict(zip(np.unique(a), bcounts[bcounts.nonzero()]))
{0: 1, 1: 3, 2: 1, 3: 1, 7: 2, 23: 1}
```

### 2.3 实战代码
注意：若绘制频数直方图，需注意bins取值为数据中的不同数个数
```
plt.figure(figsize=(15, 10))

x_major_locator=MultipleLocator(1)
#把x轴的刻度间隔设置为1，并存在变量里
ax=plt.gca()
#ax为两条坐标轴的实例
ax.xaxis.set_major_locator(x_major_locator)
#把x轴的主刻度设置为1的倍数

plt.xlim((0, 50))
plt.ylim((0, 20000))
plt.hist(list(df1[2]), bins=972)
```

&nbsp;
## 3.从字典中绘制直方图
```
plt.figure(figsize=(15, 10))

x_major_locator=MultipleLocator(1)
#把x轴的刻度间隔设置为1，并存在变量里
ax=plt.gca()
#ax为两条坐标轴的实例
ax.xaxis.set_major_locator(x_major_locator)
#把x轴的主刻度设置为1的倍数

dic={1:18,2:76,3:738,4:1197,5:1412,6:1357,7:1385,8:1508,9:2157,10:2354,11:2453,12:2853,
    13:2922,14:3017,15:3144,16:3245,17:3532,18:3799,19:3961,20:3650,21:3451,22:3477,23:3170,
     24:3430,25:3306,26:2996,27:2705,28:1058,29:4232,30:4077,31:11450,32:8150,33:10083,
    34:9916,35:8928,36:9100,37:5270,38:4955,39:5288,40:5500,41:6325,42:3908,43:4263,
    44:4872,45:4583,46:4900,47:4690,48:5530,49:5070}
    
plt.bar(dic.keys(), dic.values())#, width, color='g')
```

&nbsp;
## reference  
[matplotlib.pyplot.plot](https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.plot.html)  
[matplotlib.pyplot.plot()参数详解](https://blog.csdn.net/sinat_36219858/article/details/79800460)   
[5种方法教你用Python玩转histogram直方图](https://juejin.im/post/6844903639388012557)   
[matplotlib.pyplot.bar](https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.bar.html)
