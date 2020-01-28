## 问题
分类非常常见，但如果每个类只有几个标注样本，怎么办呢？  
大量平台用户在创建一个新对话任务时，并没有大量标注数据，每个意图往往只有几个或十几个样本。  

**面对这类问题，有一个专门的机器学习分支——Few-shot Learning 来进行研究和解决。**  
最近几年 Few-shot Learning 在图像领域的进展领先于在自然语言处理领域。  
在自然语言处理领域的研究进展以及我们对 metric-based 的方法进行系统总结后提出 few-shot learning framework。
##  Few-shot Learning 定义
人类非常擅长通过极少量的样本识别一个新物体，比如小孩子只需要书中的一些图片就可以认识什么是“斑马”，什么是“犀牛”。  
在人类的快速学习能力的启发下，研究人员希望机器学习***模型在学习了一定类别的大量数据后，对于新的类别，只需要少量的样本就能快速学习，这就是 Few-shot Learning 要解决的问题***。

***Few-shot Learning 是 Meta Learning 在监督学习领域的应用***    
**Meta Learning，又称为 learning to learn**    
**在 meta training 阶段**:将数据集分解为不同的 meta task，去学习类别变化的情况下模型的泛化能力;  
**在 meta testing 阶段**:面对全新的类别，***不需要变动已有的模型***，就可以完成分类。

形式化来说，few-shot 的训练集中包含了很多的类别，每个类别中有多个样本。  
在训练阶段，会在训练集中随机抽取 C 个类别，每个类别 K 个样本（总共 CK 个数据），构建一个 meta-task，作为模型的支撑集（support set）输入；  
再从这 C 个类中剩余的数据中抽取一批（batch）样本作为模型的预测对象（batch set）。  
即要求模型从 C*K 个数据中学会如何区分这 C 个类别，这样的任务被称为 **C-way K-shot** 问题。  

训练过程中，每次训练（episode）都会采样得到不同 meta-task。  
所以总体来看，训练包含了不同的类别组合，这种机制使得模型学会不同 meta-task 中的共性部分，比如如何提取重要特征及比较样本相似等，忘掉 meta-task 中 task 相关部分。  
通过这种学习机制学到的模型，在面对新的未见过的 meta-task 时，也能较好地进行分类。

## 在自然语言处理的研究现状
早期的 Few-shot Learning 算法研究主要集中在小样本图像识别的任务上，以 MiniImage 和 Omnigraffle 两个数据集为代表。  

近年来，在自然语言处理领域也开始出现 Few-shot Learning 的数据集和模型，相比于图像，文本的语义中包含更多的变化和噪声：  
> Gao等人提出文本与图像的一大区别在于其多样性和噪音更大，因此提出一种基于混合注意力的原型网络结构，如图 9 所示，  
  首先使用 instance-level 的 attention 从支撑集中选出和 query 更为贴近的实例，同时降低噪声实例所带来的影响。    
  然后 feature-level 的实例能够衡量特征空间中的哪些维度对分类更为重要，从而为每种不同的关系都生成相适应的距离度量函数，从而使模型能够有效处理特征稀疏的问题。  
  
  > 在现实场景当中，不同的 meta task 可能来自完全不同的领域，因此使用单独的度量方式不足以衡量所有的 meta task。  
  Yu 提出使用多种度量方式融合来解跨领域的 Few-shot Learning 问题。  
   在训练阶段，meta learner 通过任务聚类选择和结合多种度量方式来学习目标任务，不同领域的 meta task 首先通过聚类来划分，因此同一个簇内的 task 可以认为是相关的，然后在该簇中训练一个深度神经网络作为度量函数，这种机制保证了只有在同一个簇中的 task 才会共享度量函数。  
  在测试阶段，为每个 test task 使用所有度量函数的线性组合作为任务适应的度量方式。


我们团队基于目前 Metric Based 方法，提出了 Encoder-Induction-Relation 的三级框架，如图 10 所示，  
Encoder 模块用于获取每个样本的语义表示，可以使用典型的 CNN、LSTM、Transformer 等结构，  
Induction 模块用于从支撑集的样本语义中归纳出类别特征，  
Relation 模块用于度量 query 和类别之间的语义关系，进而完成分类。






## feference
[小样本学习（Few-shot Learning）综述](https://zhuanlan.zhihu.com/p/61215293)
