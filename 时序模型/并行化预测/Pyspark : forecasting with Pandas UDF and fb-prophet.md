## Pandas Udf
Pandas Udf 构建在 Apache Arrow 之上，因此具有**低开销，高性能**的特点，udf对每条记录都会操作一次，数据在 JVM 和 Python 中传输，pandas_udf就是使用 Java 和 Scala 中定义 UDF，然后在 python 中调用。

pandas udf是一个用户定义的函数，可以在Spark DataFrame上应用我们最喜欢的库，比如numpy、pandas、sklearn等等，**而不需要对语法进行任何更改，并返回Spark DataFrame**。

本案列中，我们将使用Pandas UDF的grouped_map属性，该属性允许我们将scikit-learn、statsmodels等应用到Spark Dataframe中。我们先对预测的数据按照 metric_name 和metric_section进行group by 分组，然后将fbprophet写成Pandas udf的 grouped_map函数，最后直接对group后的分组指标数据使用apply。全过程避免使用了循环等操作。


```
注意：pip install pyarrow 若报错，尝试加上版本，如pip install pyarrow==0.9.0
```
### 基于注解方式Pandas UDF
Spark 在 0.7 版中添加了 Python API，并支持user-defined functions。这些用户定义的函数一次只能操作一行，因此会遭遇高序列化和调用开销。因此，许多数据管道在 Java 和 Scala 中定义 UDF，然后从 Python 中调用它们。

基于 Apache Arrow 构建的 Pandas UDF 为您提供了两全其美的功能 - 完全用 Python 定义低开销，高性能 UDF的能力。

在 Spark 2.3 中，将会有两种类型的 Pandas UDF: 标量(scalar)和分组映射(grouped map)。Spark2.4新支持Grouped Aggregate

#### Scalar Pandas UDFs

Scalar Pandas UDFs 用于向量化标量运算。要定义一个标量 Pandas UDF，只需使用 @pandas_udf 来注释一个 Python 函数，该函数接受 pandas.Series 作为参数并返回另一个相同大小的 pandas.Series。

#### Grouped Map Pandas UDFs

Python 用户对数据分析中的 split-apply-combine 模式非常熟悉。Grouped Map Pandas UDF 是针对这种情况设计的，它们针对某些组的所有数据进行操作，例如“针对每个日期应用此操作”。

Grouped Map Pandas UDF 首先根据 groupby 运算符中指定的条件将 Spark DataFrame 分组，然后将用户定义的函数（pandas.DataFrame -> pandas.DataFrame）应用于每个组，并将结果组合并作为新的 Spark DataFrame 返回。


### 导入库和初始化设置
```
#导入库
import datetime
from dateutil.relativedelta import relativedelta
from fbprophet import Prophet
import pandas as pd
import numpy as np
import warnings
warnings.filterwarnings('ignore')
from pyspark.sql import SparkSession
from pyspark.sql.functions import pandas_udf, PandasUDFType
from pyspark.sql.types import *

#初始化
spark = SparkSession. \
    Builder(). \
    config("spark.sql.execution.arrow.enabled", "true"). \
    enableHiveSupport(). \
    getOrCreate()
```


&nbsp;
## References
[PySpark中的UDFs函数](https://blog.csdn.net/u013817676/article/details/86748386)    
[PySpark-prophet预测](https://blog.csdn.net/fitzgerald0/article/details/106157008)       
[Pyspark环境下使用fb-prophet和pandas udf时序预测](https://zhuanlan.zhihu.com/p/192772030)   
[Pyspark — forecasting with Pandas UDF and fb-prophet](https://towardsdatascience.com/pyspark-forecasting-with-pandas-udf-and-fb-prophet-e9d70f86d802)   
[Pyspark_Pandas_UDF](https://github.com/AlexWarembourg/Medium/blob/master/Pyspark_Pandas_UDF.ipynb)
