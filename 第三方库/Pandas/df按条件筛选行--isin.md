## Series.isin(self, values)
Check whether values are contained in Series.

Return a boolean Series showing whether each element in the Series matches an element in the passed sequence of values exactly.

示例：
> u_cf=pd.read_csv('user_cf_11m.csv')  
se=data['user_id']  
ldf=data[(se.isin(u_cf['user_id']) )]  
print ldf.shape
## 联想
isin方法也可以筛选指定列  
思路：先筛选符合的df行，在指定列  
df=df[(条件isin或大于小于……)]  
se=df[列名]
## reference
[pandas.Series.isin](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.isin.html)
