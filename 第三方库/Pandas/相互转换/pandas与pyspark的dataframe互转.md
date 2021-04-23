## pandas的dataframe转pyspark的dataframe
from pyspark.sql import SparkSession  
#初始化spark会话  
spark = SparkSession.builder.getOrCreate()  
spark_df = spark.createDataFrame(pandas_df)  

@nbsp;
## pyspark的dataframe转pandas的dataframe
**单机版**  
import pandas as pd  
pandas_df = spark_df.toPandas()   
**分布式版本**  
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
