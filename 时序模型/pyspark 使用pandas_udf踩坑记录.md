## 一、复现pyspark.sql.functions.pandas_udf().官方文档报错
```
py4j.protocol.Py4JJavaError: An error occurred while calling o64.showString.
failure: Lost task 2.3 in stage 8.0 (TID 30, ip-10-160-2-53.ec2.internal, executor 3): java.lang.IllegalArgumentException at
```
最后发现是pyarrow的版本过高....   
安装Pyarrow 0.14.1或者更低版本就可以解决这个问题了

Pyarrow rolled out a new version 0.15 on october 5,2019 which causes pandas Udf to throw error. Spark needs to upgrade to be compatible with this(which might take some time).

&nbsp;
## pyarrow.lib.ArrowInvalid: ‘utf-32-le‘ codec can‘t decode
使用pyspark的定义数据类型后，通过@pandas_udf的形式进行装饰的时候发现错误。
```
schema = StructType([
    StructField("store_sku", StringType()),
    StructField("ds", StringType()),
    StructField("pro_pred", DoubleType())
])

@pandas_udf(schema, functionType=PandasUDFType.GROUPED_MAP)
```
发现出现以下异常
```
pyarrow.lib.ArrowInvalid: ‘utf-32-le’ codec can’t decode bytes in position 0-3: code point not in range(0x110000)
```
经过排查是ds字段的问题，上面定义ds的数据类型为StringType
但是pandas跑出来为datetime。
解决办法如下：
```
df['ds']=df['ds'].astype(str)
```

&nbsp;
## References
[Pandas scalar UDF failing, IllegalArgumentException](https://stackoverflow.com/questions/58458415/pandas-scalar-udf-failing-illegalargumentexception?newreg=0945d0b1ec2e434d96c8d76f55792a30)  
[pyspark 使用pandas_udf时的一个坑](https://www.huaweicloud.com/articles/5a92708ba7320b28b9caedb1c6eebb42.html)  
[pyarrow.lib.ArrowInvalid: ‘utf-32-le‘ codec can‘t decode](https://blog.csdn.net/fitzgerald0/article/details/106106663)
