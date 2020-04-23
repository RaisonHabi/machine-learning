## 统计多列元素的出现情况
```
gp=df.groupby(by=['A','B'])
gp.size()
```
类型：DataFrameGroupBy

&nbsp;
## DataFrameGroupBy转成DataFrame
```
newdf=gp.size()
newdf.reset_index(name='times')
```
其中name中参数就是我们可以为最后一列添加新的名字，例如这里的“times”  
这个时候newdf已经是DataFrame的类型了

&nbsp;
## reference  
[pandas.DataFrame.groupby](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html)  
[pandas python 分组统计的方法](https://blog.csdn.net/qq_22238533/article/details/72367792)
