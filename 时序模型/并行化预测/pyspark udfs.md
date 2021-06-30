## 一、pyspark udfs
我们在用python原生的函数来处理迭代我们的数据，但是我们发现在处理一个比较大的dataframe可能会花费我们很久的时间。   
所以如果我们拥有一个集群，那么如何在集群上通过Pyspark来加速我们的处理速度呢？换句话说我们如何将python函数转化成Spark的用户自定义函数(UDF)呢?
### 基于注解方式Pandas UDF
Spark 在 0.7 版中添加了 Python API，并支持user-defined functions。这些用户定义的函数一次只能操作一行，因此会遭遇高序列化和调用开销。因此，许多数据管道在 Java 和 Scala 中定义 UDF，然后从 Python 中调用它们。

基于 Apache Arrow 构建的 Pandas UDF 为您提供了两全其美的功能 - 完全用 Python 定义低开销，高性能 UDF的能力。

在 Spark 2.3 中，将会有两种类型的 Pandas UDF: 标量(scalar)和分组映射(grouped map)。Spark2.4新支持Grouped Aggregate

#### Scalar Pandas UDFs

Scalar Pandas UDFs 用于向量化标量运算。要定义一个标量 Pandas UDF，只需使用 @pandas_udf 来注释一个 Python 函数，该函数接受 pandas.Series 作为参数并返回另一个相同大小的 pandas.Series。

#### Grouped Map Pandas UDFs

Python 用户对数据分析中的 split-apply-combine 模式非常熟悉。Grouped Map Pandas UDF 是针对这种情况设计的，它们针对某些组的所有数据进行操作，例如“针对每个日期应用此操作”。

Grouped Map Pandas UDF 首先根据 groupby 运算符中指定的条件将 Spark DataFrame 分组，然后将用户定义的函数（pandas.DataFrame -> pandas.DataFrame）应用于每个组，并将结果组合并作为新的 Spark DataFrame 返回。
### pyspark.sql.types
```
__all__ = [
    "DataType", "NullType", "StringType", "BinaryType", "BooleanType", "DateType",
    "TimestampType", "DecimalType", "DoubleType", "FloatType", "ByteType", "IntegerType",
    "LongType", "ShortType", "ArrayType", "MapType", "StructField", "StructType"]
```

&nbsp;
## 二、prophet/arima实例(jupyter notebook)
```
import sys
import os
sys.path.append(os.environ.get('SPARK_HOME')+'/python/lib/py4j-0.10.7-src.zip')
sys.path.append(os.environ.get('SPARK_HOME')+'/python/')
from pyspark import SparkConf
from pyspark.sql.session import SparkSession
conf = SparkConf()
conf.set('master','yarn')
# 设置自己有权限的队列，spark executor相关参数
conf.set('spark.yarn.queue','xxx')
conf.set('spark.pyspark.python','./Python/bin/python')
# 默认executor是python2的环境，而且没有其它numpy等相关的模块，需要用户自己上传python环境，注意这里自定义的python版本需要和notebook的python版本保持一致，路径jupyter的目录
# 非分布式map操作可以注掉这个代码
conf.set('spark.yarn.dist.archives','xxx')
# 开启apache arrow（Pandas Udf 构建在 Apache Arrow 之上，因此具有低开销，高性能的特点）
conf.set("spark.sql.execution.arrow.enabled", "true")
os.environ['PYSPARK_PYTHON']='./Python/bin/python'
os.environ['PYSPARK_DRIVER_PYTHON']='./Python/bin/python'
spark = SparkSession.builder.config(conf=conf).enableHiveSupport().getOrCreate()
```
```
from time import time
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from pyspark.sql.functions import pandas_udf, PandasUDFType
from pyspark.sql.types import *
```
### apply调用pandas udf 使用prophet模型
```
from fbprophet import Prophet


# 节假日数据
xxx
# 合并所有节假日，作为Prophet的形参
holidays=pd.concat(xxx)


# 预测函数
def prophet_pred(data_sort):
    m=Prophet(holidays=holidays,holidays_prior_scale=10.0,changepoint_prior_scale=0.15,changepoint_range = 0.8, \
              daily_seasonality=True,weekly_seasonality=True,yearly_seasonality=True)
    m.fit(data_sort)
    future=m.make_future_dataframe(periods=1,freq='W')
    forecast=m.predict(future)

    return forecast.tail()

# pandas_udf
schema = StructType([
 xxx
])
@pandas_udf(schema, functionType=PandasUDFType.GROUPED_MAP)
def run_model(pdf):
    def run(pdf,cut_off):
        data_sort=pdf.query('xxx < @cut_off').sort_values('xxx')
        xxx
        
        df = prophet_pred(data_sort)
        
        xxx
        # 类型转换：原df['xxx']类型为datetime，需转换同schema类型string一致
        df['xxx']=df['xxx'].astype(str)
        cols=[xxx] 
        
        return df[cols]
    
    return run(pdf,cut_off)
```
```
# 
t0=time()

data = spark.sql("""
xxx
""")

rtn_predict = data.groupby('xxx').apply(run_model)

print('done! cost time:',time()-t0)
```
### apply调用pandas udf 使用arima模型
```
import pmdarima as pm
from pmdarima import model_selection
import datetime 

# 预测函数
def arima_pred(data_sort,cut_off):
    modl = pm.auto_arima(data_sort['y'])
    fc, conf_int = modl.predict(n_periods=5, return_conf_int=True)
    fc_lst=fc.tolist()
    df=pd.DataFrame(columns=[xxx])
    for i in range(len(fc_lst)):
        y_tmp=round(fc_lst[i],7)      
        cut_off_dt=datetime.datetime.strptime(cut_off,'%Y-%m-%d')
        delta = datetime.timedelta(days=i*7)
        ds_tmp = cut_off_dt + delta
        ds=ds_tmp.strftime('%Y-%m-%d')
        df=df.append([{xxx}], ignore_index=True)
    
    return df
    
# pandas_udf
schema = StructType([
    xxx
])
@pandas_udf(schema, functionType=PandasUDFType.GROUPED_MAP)
def run_model(pdf):
    def run(pdf,cut_off):
        data_sort=pdf.query('xxx < @cut_off').sort_values('xxx')
        xxx
        
        df=arima_pred(data_sort,cut_off)
        
        xxx
        cols=[xxx] 
        
        return df[cols]
    
    return run(pdf,cut_off)
```
```
t0=time()
cut_off=xxx

data = spark.sql("""
xxx
""")

rtn_predict = data.groupby('xxx').apply(run_model)

print('done! cost time:',time()-t0)
```

&nbsp;
## 三、prophet/arima实例（定时任务）
定时任务一般需部署pyspark任务：调用py脚本，并指定输入、输出及其他变量等参数

&nbsp;
## References
[PySpark中的UDFs函数](https://blog.csdn.net/u013817676/article/details/86748386)  
[Source code for pyspark.sql.types](https://spark.apache.org/docs/2.1.2/api/python/_modules/pyspark/sql/types.html)   
[pyspark.sql.functions.pandas_udf(f=None, returnType=None, functionType=None)](https://spark.apache.org/docs/3.0.0-preview/api/python/pyspark.sql.html#pyspark.sql.functions.pandas_udf)   
[apply(udf)](https://spark.apache.org/docs/3.0.0-preview/api/python/pyspark.sql.html#pyspark.sql.GroupedData.apply)
