## 简介
**问题**：  
有时你通过 Python’s Pandas 打开一个大的数据集，然后试着去获取一些度量标准，但这时整个过程可能会突然停止。  
如果你使用 Pandas 处理大数据，可能一个简单的序列平均值都需要你等待一分钟，我们甚至不会去调用 apply。这还只是百万级别的行数！当你的数据达到亿级别时，你最好使用 Spark 或者其他方式。

我在不久之前发现了这个工具：不需要更好的基础架构或转换语言，就可以在 Python 中加速数据分析的方法。  
但是如果数据集太大，它的最终优化结果会一定的限制，但是它仍然比常规的 Pandas 扩展性好，可能也更符合你的问题场景 —— 尤其是不进行大量的重写索引时。


**Dask 是一个开源项目，为你提供 NumPy 数组、Pandas Dataframes 以及常规 list 的抽象，允许你使用多核处理器并行运行它们的操作**。
## 文档
Dask 提供了更高级别的 Array、Bag、和 DataFrame 集合，它们模仿 NumPy、list 和 Pandas，但允许在不适合主内存的数据集上并行操作。  
对于大型数据集，Dask 的高级集合可以取代 NumPy 和 Pandas。

> 操作大型数据集，即使这些数据不适用于内存  
使用尽可能多的内核来加速长时间计算  
在大的数据集上，通过标准的 Pandas 操作，如集群、连接、还有时间序列计算，来对计算做分布式处理。
## 使用 Dask Dataframes
为了生成一个 Dask Dataframe，你可以像在 Pandas 中那样简单调用 read_csv 方法，或只调用给定的一个 Pandas Dataframe df。

**dd = ddf.from_pandas(df, npartitions=N)**    
ddf 是你使用 DASK Dataframes 导入的名称，而 nparitions 是一个参数，它告诉 Dataframe 你期望如何对它进行分区。  
StackOverflow，建议将 Dataframe 划分到你**计算机内核数目相同的分区中，或是这个数字的几倍**，因为每个分区都会运行在不同的线程上，如果有太多线程，它们之间将变得过于昂贵。


## reference
[[译] 在 Python 中，如何运用 Dask 数据进行并行数据分析](https://juejin.im/post/5c1feeaf5188257f9242b65c)  
[Dask](https://docs.dask.org/en/latest/dataframe.html)  
[Dask 介绍](https://www.cnblogs.com/hellogreen/p/8817863.html)
