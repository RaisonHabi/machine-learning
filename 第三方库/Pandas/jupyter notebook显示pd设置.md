## 显示所有数据
### 显示所有列
```
pd.set_option('display.max_columns', None)
```
### 显示所有行
```
pd.set_option('display.max_rows', None)
```

&nbsp;
## 显示固定行列数
### 显示1000列
```
pd.set_option(‘max_columns’,1000) 
```
### 显示1000行
```
pd.set_option(‘max_row’,1000) 
```

&nbsp;
## 设置value的显示长度为100，默认为50
```
pd.set_option('max_colwidth',100)
```

&nbsp;
## 设置浮点数
```
pd.set_option(‘display.float_format’, lambda x: ‘%.5f’ % x)
```

&nbsp;
## reference
[jupyter notebook显示所有列](https://blog.csdn.net/yyy430/article/details/84935669)
