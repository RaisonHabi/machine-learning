## 数据分组 pd.cut
示例代码
```
import pandas as pd

bins=[0,380,400,420,440,460,480,500,520,540,560,580,600,1000]
seg1=pd.cut(data[1],bins,right=False)
pd.value_counts(seg1)
>>>
[400, 420)     1170
[420, 440)     1168
[440, 460)     1007
[380, 400)      955
[0, 380)        921
[460, 480)      783
[480, 500)      562
[500, 520)      471
[520, 540)      352
[540, 560)      200
[560, 580)      104
[580, 600)       54
[600, 1000)      23
Name: 1, dtype: int64
```

&nbsp;
## 可视化-直方图plt.hist
```
%matplotlib inline
import matplotlib.pyplot as plt

plt.hist(data[1], bins=10)
```
### 示例
```
plt.hist(X[7], bins=10)

plt.hist((df_1[5],df_0[5]),bins=10)
或plt.hist([df_1[5],df_0[5]],bins=10)
```

&nbsp;
## reference
[matplotlib.pyplot.hist](https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.hist.html)  
