## df.DataFrame
class pandas.DataFrame(data=None, index=None, columns=None, dtype=None, copy=False)  
Parameters:  
> **data :** *ndarray (structured or homogeneous), Iterable, dict, or DataFrame*  
Dict can contain Series, arrays, constants, or list-like objects  
**index :** ***Index or array-like***    
Index to use for resulting frame. Will default to RangeIndex if no indexing information part of input data and no index provided  
**columns :** ***Index or array-like***    
Column labels to use for resulting frame. Will default to **RangeIndex (0, 1, 2, …, n)** if no column labels are provided  
**dtype :** *dtype, default None*  
Data type to force. Only a single dtype is allowed. If None, infer  
**copy :** *boolean, default False*  
Copy data from inputs. Only affects DataFrame / 2d ndarray input
## loc、 iloc
**loc：**	Access ***a group of rows and columns by label(s) or a boolean array.***    
*.loc[] is primarily label based, but may also be used with a boolean array.*  

**iloc：**	***Purely integer-location based indexing for selection by position.***      
*.iloc[] is primarily integer position based (**from 0 to length-1 of the axis**), but may also be used with a boolean array.*  


## ix
A primarily label-location based indexer, with integer position fallback.    
*Warning: Starting in 0.20.0, the .ix indexer is deprecated, in favor of the more strict .iloc and .loc indexers.*  
.ix[] supports mixed integer and label based access. It is primarily label based, but will fall back to integer positional access unless the corresponding axis is of integer type.


## at、 iat
**at：** ***Access a single value for a row/column label pair.***  
*Similar to 'loc', in that both provide label-based lookups.*  
***Use 'at' if you only need to get or set a single value in a DataFrame or Series.***



**iat:** ***Access a single value for a row/column pair by integer position.***  
*Similar to 'iloc', in that both provide integer-based lookups.*  
***Use 'iat' if you only need to get or set a single value in a DataFrame or Series.***




## df
df[]只能进行行选择，或列选择，不能同时进行列选择，列选择只能是列名。行号和区间选择只能进行行选择  
示例：  
> df = pd.DataFrame([[1, 2,3], [4, 5,6], [7, 8,9],[1,2,3]],index=('d','e','f','g'),columns=['a','b','c'])  
df[0:3] #选取多行   
df['a'] 等价于 **df.a** #选取一列   ***#不能使用df.选择行***  
**df[['a','c']]** #选取多列  
df['f':] 等价于 df[2:] **#行号和区间索引只能用于行**
# ps
#### 1.当index为整数时，实践中loc[index,列名]，当index取0-9时正常，取10时报错（keyerror），尽管包含10的index
#### 2.实践中at的查询、修改更快一些，如上方法所推荐的
## reference
[pandas.DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)  
[简单两步，大幅提高python数据处理速度](https://zhuanlan.zhihu.com/p/29362983)  
[Pandas DataFrame的loc、iloc、ix和at/iat浅析](https://www.jianshu.com/p/199a653e9668)  
[pandas.DataFrame.loc](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.loc.html)
