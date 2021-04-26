## pandas的dataframe转pyspark的dataframe
from pyspark.sql import SparkSession  
#初始化spark会话  
spark = SparkSession.builder.getOrCreate()  
spark_df = spark.createDataFrame(pandas_df)  

@nbsp;
## pyspark的dataframe转pandas的dataframe
### 单机版
```
import pandas as pd  
pandas_df = spark_df.toPandas()  
```
#### toPandas()性能优化
Using Arrow to Optimize Conversion

应用了Arrow就不一样，原文作者的原话：Because Arrow defines a common data format across different language implementations, it is possible to transfer data from Java to Python without any conversions or processing. ,Apache Arrow：一个跨平台的在内存中以列式存储的数据层，用来加速大数据分析速度。其可以一次性传入更大块的数据，pyspark中已经有载入该模块，需要打开该设置：
```
spark.conf.set("spark.sql.execution.arrow.enabled", "true")
```
原文链接：https://blog.csdn.net/sinat_26917383/article/details/80929492

### 分布式版本
```
import pandas as pd  
def _map_to_pandas(rdds):  
    return [pd.DataFrame(list(rdds))]  
    
def topas(df, n_partitions=None):  
    if n_partitions is not None: df = df.repartition(n_partitions)  
    df_pand = df.rdd.mapPartitions(_map_to_pandas).collect()  
    df_pand = pd.concat(df_pand)  
    df_pand.columns = df.columns  
    return df_pand  
    
pandas_df = topas(spark_df)  
```

&nbsp;
## reference
[pandas和spark的dataframe互转](https://www.cnblogs.com/TTyb/p/9996091.html)  
[在spark dataFrame 中使用 pandas dataframe](https://www.jianshu.com/p/16e3c0ad7bc7)
