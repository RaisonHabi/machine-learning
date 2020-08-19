**详见 深度学习/框架对比**   
TensorFlow： 
将计算流程表示成图； 
通过Sessions来执行图计算； 
将数据表示为tensors； 
使用Variables来保持状态信息； 
分别使用feeds和fetches来填充数据和抓取任意的操作结果； 

TensorFlow 用图来表示计算任务，图中的节点被称之为operation，缩写成op。 
一个节点获得 0 个或者多个张量 tensor，执行计算，产生0个或多个张量。 
图必须在会话(Session)里被启动，会话(Session)将图的op分发到CPU或GPU之类的设备上，同时提供执行op的方法，这些方法执行后，将产生的张量(tensor)返回。 

常值张量(constant)。 
变量 Variable，是维护图执行过程中的状态信息的. 需要它来保持和更新参数值，是需要动态调整的。Tensorflow 的变量必须先初始化，然后才有值！ 


Pytorch 最大的优点是建立的神经网络是动态的，比静态的tensorflow能有效处理一些问题，比如rnn变化时间长度的输出。
