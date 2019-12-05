## drop函数
**用法：**  
DataFrame.drop(labels=None,axis=0, index=None, columns=None, inplace=False)  
**参数说明：**  
> labels 就是要删除的行列的名字，用列表给定  
axis 默认为0，指删除行，因此删除columns时要指定axis=1；  
index 直接指定要删除的行  
columns 直接指定要删除的列  
inplace=False，默认该删除操作不改变原数据，而是返回一个执行删除操作后的新dataframe；  
inplace=True，则会直接在原数据上进行删除操作，删除后无法返回。   

[Python中pandas dataframe删除一行或一列：drop函数](https://blog.csdn.net/songyunli1111/article/details/79306639)
