## TonY 是如何工作的
类似于 MapReduce 提供的在 Hadoop 上运行 Pig/Hive 脚本的引擎，Spark 提供使用 Spark API 运行 scala 代码的引擎，TonY 旨在通过处理资源协商和容器环境设置等任务，为在 Hadoop 上运行 TensorFlow 作业提供同样顶级的支持。

在 YARN 的 TonY 上运行 TensorFlow
```
1. TonY 主要包含三个要素：Client、ApplicationMaster 和 TaskExecutor。运行 TonY 作业的端到端处理过程如下：
2. 用户向 Client 提交 TensorFlow 模型训练代码、参数及其 Python 虚拟环境（包含 TensorFlow 依赖）。
3. Client 设置 ApplicationMaster（AM）并将其提交给 YARN 集群。
4. AM 与 YARN 基于用户资源请求的资源管理（Resource Manager）进行资源协商（参数服务器及线程、内存和 GPU 的数量）。
5. 一旦 AM 收到分配，它就会在分配的节点上生成 TaskExecutor。
6. TaskExecutor 启动用户的训练代码并等待其完成。
7. 用户的训练代码启动，TonY 定期在 TaskExecutor 和 AM 之间跳动，以检查其活性。
```

&nbsp;
## References
[领英开源TonY：构建在Hadoop YARN上的TensorFlow框架](https://www.jiqizhixin.com/articles/2018-09-20-6)
