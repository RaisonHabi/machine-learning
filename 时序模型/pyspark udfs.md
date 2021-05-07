## pyspark udfs
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
## References
[PySpark中的UDFs函数](https://blog.csdn.net/u013817676/article/details/86748386)  
[Source code for pyspark.sql.types](https://spark.apache.org/docs/2.1.2/api/python/_modules/pyspark/sql/types.html)
