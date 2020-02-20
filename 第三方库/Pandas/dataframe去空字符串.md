## 问题  
在处理数据的时候遇到一个问题是，明明某些列有很多是空的，
但是在python里用dataframe.info统计出来并不是空的，就很奇怪，排查之后发现在我的excel表里这些数据看起来是空的值，但其实是一个空格。

但是用isnull判断是为False的
## pandas的空值   
在pandas里空值是指NA，包括numpy的np.nan,python的None。  
pandas对空值进行操作可以用isnull／notnull／isna／notna／fillna／dropna等等。  

**但是，这些操作对空字符串均无效**。  
空字符串即“ ”（一个或多个空格），但在excel表格里其实是看不出来，pandas也把它当成有值进行操作。  
## 正则匹配空格替换
DataFrame.**replace(to_replace=None, value=None, inplace=False, limit=None, regex=False, method='pad')**    
Replace values given in to_replace with value.

示例：  
> df.replace(to_replace=r'^\s*$',value=np.nan,regex=True,inplace=True)
其中\s表示空白字符，匹配任何空白字符，包括空格、制表符、换页符等, *表示任意个

> df.replace(to_replace=r'\\N',value=np.nan,regex=True,inplace=True)


## reference
[【pandas】dataframe去空字符串处理](https://blog.csdn.net/maotianyi941005/article/details/84315965)
