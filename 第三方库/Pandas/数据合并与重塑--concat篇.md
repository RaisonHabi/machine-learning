## concat 
concat函数是在pandas底下的方法，可以将数据根据不同的轴作简单的融合  
pd.concat(objs, axis=0, join='outer', join_axes=None, ignore_index=False, keys=None, levels=None, names=None, verify_integrity=False)  
**参数说明**  
> objs: series，dataframe或者是panel构成的序列lsit   
axis： 需要合并链接的轴，0是行，1是列   
join：连接的方式 inner，或者outer  

[PANDAS 数据合并与重塑（concat篇）](https://blog.csdn.net/stevenkwong/article/details/52528616)
