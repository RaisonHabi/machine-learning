tf分布式训练：https://zhuanlan.zhihu.com/p/56991108 

模型并行称之为"in-graph replication"， 
由于模型分割开的各个部分之间有相互依赖关系，因此计算效率不高。所以在模型大小不算太大的情况下一般不使用模型并行。 

数据并行称之为"between-graph replication"，相比较模型并行，数据并行方式能够支持更大的训练规模，提供更好的扩展性，因此数据并行是深度学习最常采用的分布式训练策略。 

深度学习模型的训练是一个迭代的过程。在每一轮迭代中，前向传播算法会根据当前参数的取值计算出在一小部分训练数据上的预测值，然后反向传播算法再根据损失函数计算参数的梯度并更新参数。 

数据并行可以是同步的（synchronous），也可以是异步的（asynchronous）   
在tensorflow中异步训练是默认的并行训练模式。 

1.可以简单地认为异步模式就是单机模式复制了多份，每一份使用不同的训练数据进行训练。 

异步训练总体会训练速度会快很多，但是异步训练的一个很严重的问题是梯度失效问题（stale gradients），刚开始所有设备采用相同的参数来训练，但是异步情况下，某个设备完成一步训练后，可能发现模型参数已经被其它设备更新过了，此时这个设备计算出的梯度就过期了。由于梯度失效问题，异步训练可能陷入次优解（sub-optimal training performance）。 


2.同步指的是所有的设备都是采用相同的模型参数来训练，等待所有设备的mini-batch训练完成后，收集它们的梯度后执行模型的一次参数更新。在同步模式下，所有的设备同时读取参数的取值，并且当反向传播算法完成之后同步更新参数的取值。 

同步模式相当于通过聚合多个设备上的mini-batch形成一个更大的batch来训练模型，相对于异步模式，在同步模型下根据并行的worker数量线性增加学习速率会取得不错的效果。如果使用tensorflow estimator接口来分布式训练模型的话，在同步模式下需要适当减少训练步数（相对于采用异步模式来说），否则需要花费较长的训练时间。Tensorflow estimator接口唯一支持的停止训练的条件就全局训练步数达到指定的max_steps。 

3.小结： 
同步训练看起来很不错，但是实际上需要各个设备的计算能力要均衡，而且要求集群的通信也要均衡，类似于木桶效应，一个拖油瓶会严重拖慢训练进度，所以同步训练方式相对来说训练速度会慢一些。 

虽然异步模式理论上存在缺陷，但因为训练深度学习模型时使用的随机梯度下降本身就是梯度下降的一个近似解法，而且即使是梯度下降也无法保证达到全局最优值。在实际应用中，在相同时间内使用异步模式训练的模型不一定比同步模式差。所以这两种训练模式在实践中都有非常广泛的应用。 




分布式训练架构： 
1.Parameter Server架构 
Parameter server架构（PS架构）是深度学习最常采用的分布式训练架构。在PS架构中，集群中的节点被分为两类：parameter server和worker。其中parameter server存放模型的参数，而worker负责计算参数的梯度。在每个迭代过程，worker从parameter sever中获得参数，然后将计算的梯度返回给parameter server，parameter server聚合从worker传回的梯度，然后更新参数，并将新的参数广播给worker。 

2.Ring AllReduce架构 
PS架构中，当worker数量较多时，ps节点的网络带宽将成为系统的瓶颈。 

Ring AllReduce架构中各个设备都是worker，没有中心节点来聚合所有worker计算的梯度。Ring AllReduce算法将 device 放置在一个逻辑环路（logical ring）中。每个 device 从上行的device 接收数据，并向下行的 deivce 发送数据，因此可以充分利用每个 device 的上下行带宽。 

使用 Ring Allreduce 算法进行某个稠密梯度的平均值的基本过程如下： 
将每个设备上的梯度 tensor 切分成长度大致相等的 num_devices 个分片； 
ScatterReduce 阶段：通过 num_devices - 1 轮通信和相加，在每个 device 上都计算出一个 tensor 分片的和； 
AllGather 阶段：通过 num_devices - 1 轮通信和覆盖，将上个阶段计算出的每个 tensor 分片的和广播到其他 device； 
在每个设备上合并分片，得到梯度和，然后除以 num_devices，得到平均梯度。 



相比PS架构，Ring Allreduce架构是带宽优化的，因为集群中每个节点的带宽都被充分利用。此外，在深度学习训练过程中，计算梯度采用BP算法，其特点是后面层的梯度先被计算，而前面层的梯度慢于前面层，Ring-allreduce架构可以充分利用这个特点，在前面层梯度计算的同时进行后面层梯度的传递，从而进一步减少训练时间。 
Ring Allreduce的训练速度基本上线性正比于GPUs数目（worker数）。




Tensorflow 的简单 profiling： 
为了了解训练过程中 GPU/CPU 的使用情况，我们可以使用 Cuda 提供的 cupti 来对 GPU 运行情况进行 profiling。我们可以从 tensorflow 中的函数调用这个库并打印出 profile 的 log。 


数据并行和模型并行： 
深度学习模型的并行有两种方案：模型并行(model parallel)和数据并行(data parallel)。 
假设我们有多个 node： 
模型并行：不同 node 输入相同数据，运行模型的不同部分 
数据并行：不同 node 输入不同数据，运行相同的完整的模型。 

为了完成一次更新，不同 node 间需要交换 forward 和 backprop 的信息，所以通信数据量是选择这两种并行方案的一个考量因素。另一个考量因素是由数据依赖(data dependency)带来的计算的阻塞(blocking)。最后一个考量因素是内存限制。当模型参数以及计算产生的中间变量无法放入一个 node 的内存时，我们只能使用模型并行。 
当参数量巨大的时候，数据通信量会成为模型运行的瓶颈。模型并行的数据通信量可以比数据并行更少。 

数据并行的方法：Parameter server（ps） 模式、Allreduce 模式 

FP16提速：FP16 也是一个比较流行的提速方案，GPU计算速度(P100, V100, Titan V 中有大量 FP16 计算单元)的提速以及变量传输的提速。 


序列模型的实现细节： 
1.数据预处理的加速： 
数据预处理的过程即从原始数据到将切分好的 mini-batch 载入模型的整个过程。tensorflow 的文档将整个流程划分为 Extract，Transform 和 Load(ETL)。 

tensorflow 最初推荐使用的是 tf.placeholder 模式。这个模式的流程如下： 
Extract：先将整个数据集读入内存 
Transform：在Python中将数据转化成 numpy 的矩阵 
Load：使用 tf.placeholder 将 numpy 数据拷贝到 tensorflow 的内存中，再从 tensorflow 拷贝到 GPU 中。 

为了解决 tf.placeholder 的效率问题，tensorflow 采用了 cache 和 pipeline 的思想。 

2.变长序列的处理： 
处理 mini-batch 中的序列长度变化：Padding 和 Bucketing 


大 batch size 带来的泛化性能低的现象。分析了一个主要原因：更新量不足，并介绍了两种解决方案：将更新步数和小 batch size 对齐，以及增大 learning rate。增大 learning rate 符合训练加速的期望，但会带来训练稳定性和泛化性能的下降， 经验上可以通过 learning rate warmup 以及对各层参数的梯度进行归一化来缓解。 
在保证泛化性能的前提下， batch size 最大只能提高到 8096 ？ 





