## 工作方式
**pandas**  
单机single machine tool，没有并行机制parallelism，不支持Hadoop，处理大量数据有瓶颈  
**pyspark**  
分布式并行计算框架，内建并行机制parallelism，所有的数据和操作自动并行分布在各个集群结点上。  
以处理in-memory数据的方式处理distributed数据。支持Hadoop，能处理大量数据。
## 延迟机制
**pandas**  
not lazy-evaluated  
**pyspark**  
lazy-evaluated
## 内存缓存
**pandas**  
单机缓存  
**pyspark**  
persist() or cache()将转换的RDDs保存在内存
## DataFrame可变性
**pandas**  
Pandas中DataFrame是可变的  
**pyspark**  
Spark中RDDs是不可变的，因此DataFrame也是不可变的
## 创建  
**pandas**  
从spark_df转换：pandas_df = spark_df.toPandas()，或读取其他数据  
**pyspark**  
从pandasdf转换：spark_df = SQLContext.createDataFrame(pandas_df)  
另外，createDataFrame支持从list转换sparkdf，其中list元素可以为tuple，dict，rdd
##  两者互相转换
pandas_df = spark_df.toPandas()   
spark_df = spark.createDataFrame(pandas_df)
## index索引  
**pandas**  
自动创建  
**pyspark**  
没有index索引，若需要则要额外创建该列
## 行结构 
**pandas**  
Series结构，属于Pandas DataFrame结构  
**pyspark**  
Row结构，属于Spark DataFrame结构
## 列结构
**pandas**  
Series结构，属于Pandas DataFrame结构  
**pyspark**  
Column结构，属于Spark DataFrame结构，如：DataFrame[name: string]
## 列名称
**pandas**  
不允许重名  
**pyspark**  
允许重名,修改列名采用alias方法



## reference
[pyspark系列--pandas与pyspark对比](https://zhuanlan.zhihu.com/p/34901585)
