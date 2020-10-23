## 两个不同列表转换成为数据框
```
from pandas.core.frame import DataFrame
a=[1,2,3,4]#列表a
b=[5,6,7,8]#列表b
c={"a" : a,
   "b" : b}#将列表a，b转换成字典
data=DataFrame(c)#将字典转换成为数据框
print(data)
```

&nbsp;
## reference
[Pandas将列表（List）转换为数据框（Dataframe）](https://blog.csdn.net/claroja/article/details/64439735)
