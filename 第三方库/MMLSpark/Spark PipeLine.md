## Spark PipeLine
是基于DataFrames的高层的API，可以方便用户构建和调试机器学习流水线
可以使得多个机器学习算法顺序执行，达到高效的数据处理的目的
 
DataFrame是来自Spark SQL的ML DataSet 可以存储一系列的数据类型，text,特征向量，Label和预测结果
 
### Transformer
将DataFrame转化为另外一个DataFrame的算法，通过实现transform()方法

### Estimator
将DataFrame转化为一个Transformer的算法，通过实现fit()方法
 
### PipeLine
将多个Transformer和Estimator串成一个特定的ML Wolkflow

### Parameter
Tansformer和Estimator共用同一个声明参数的API
 
#### Transformer和Estimator是PipeLine的Stage, Pipeline是一系列的Stage按照声明的顺序排列成的工作流
 
Transformer.transform()和Estimator.fit()都是无状态的
每一个Transformer和Estimator的实例都有唯一的ID在声明参数的时候非常有用
 
下面是一个线性的PipeLine的流程......

## References
[Spark.ML之PipeLine学习笔记](https://www.cnblogs.com/kongchung/p/5776727.html)  
[ML Pipelines](http://spark.apache.org/docs/2.0.0/ml-pipeline.html)
