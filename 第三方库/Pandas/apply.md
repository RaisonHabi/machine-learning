## DataFrame.apply(self, func, axis=0, raw=False, result_type=None, args=(), **kwds)
Parameters  
> ***funcfunction***    
Function to apply to each column or row.

> ***axis{0 or ‘index’, 1 or ‘columns’}, default 0***    
Axis along which the function is applied:  
**0 or ‘index’: apply function to each column**.    
**1 or ‘columns’: apply function to each row**.  
其他参数详见链接  
## 示例  
**apply 结合lambda if else**  
实现：根据dpd列的值是否为7，新增label列为1或0  
df['label']=df['dpd'].apply(lambda x: 1 if x==7 else 0)
## reference
[pandas.DataFrame.apply](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.apply.html)
