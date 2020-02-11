## 示例参考链接
## 默认情况下按相同列名合并
pd.merge(df1,df2))
## 显示指定进行合并的列名
#### 双方有公共列名
pd.merge(df1,df2,on='key')
#### 双方无公共列名
## 根据多个列名进行合并
## 指定合并的方式：inner、outer、left、right
## 处理重复列名
## reference
[Pandas：按列合并数据集--merge函数](https://blog.csdn.net/bqw18744018044/article/details/79944544)
