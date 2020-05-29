## online-learning
### 在线学习与离线学习 
**offline learning and online learning**     
> In offline learning, **the whole training data must be available at the time of model training**. Only when training is completed can the model be used for predicting.   
	In contrast, online algorithms process data sequentially. They produce a model and put it in operation without having the complete training dataset available at the beginning. **The model is continuously updated during operation as more training data arrives**.

&nbsp;
### 增量学习与减量学习
**online learning包括了incremental learning和decremental learning**  

incremental learning增量学习，是指**一个学习系统能不断地从新样本中学习新的知识,并能保存大部分以前已经学习到的知识**。  
增量学习非常类似于人类自身的学习模式。因为人在成长过程中,每天学习和接收新的事物,学习是逐步进行的,而且,对已经学习到的知识,人类一般是不会遗忘的。  
Less restrictive than online algorithms are incremental algorithms that process input examples one by one (or batch by batch) and update the decision model after receiving each example. Incremental algorithms may have random access to previous examples or representative/selected examples. In such a case, these algorithms are called in- cremental algorithms with partial memory. Typically, in incremental algorithms, for any new presentation of data, the update operation of the model is based on the previous one. Streaming algorithms are online algorithms for processing high-speed continuous flows of data. In streaming, examples are processed sequentially as well and can be examined in only a few passes (typically just one). These algorithms use limited memory and limited processing time per item.
```
一个增量学习算法应同时具有以下特点:
    1)可以从新数据中学习新知识;  
    2)以前已经处理过的数据不需要重复处理;
    3)每次只有一个训练观测样本被看到和学习;
    4)学习新知识的同时能保存以前学习到的大部分知识;
    5)—旦学习完成后训练观测样本被丢弃;
    6)学习系统没有关于整个训练样本的先验知识;
```
增量式算法：就是每当新增数据时，并不需要重建所有的知识库，而是在原有知识库的基础上，仅做由于新增数据所引起的更新，这更加符合人的思维原理。

decremental learning递减学习，即抛弃“价值最低”的保留的训练样本。这两个概念在incremental and decremental svm这篇论文里面可以看到具体的操作过程。

&nbsp;
### 主动学习与直推学习
主动学习(active learning)，指的是这样一种学习方法：
有的时候，有类标的数据比较稀少而没有类标的数据是相当丰富的，但是对数据进行人工标注又非常昂贵，这时候，学习算法可以主动地提出一些标注请求，将一些经过筛选的数据提交给专家进行标注。  
这个筛选过程也就是主动学习主要研究的地方了，怎么样筛选数据才能使得请求标注的次数尽量少而最终的结果又尽量好。
主动学习的过程大致是这样的，有一个已经标好类标的数据集K(初始时可能为空)，和还没有标记的数据集U，通过K集合的信息，找出一个U的子集C，提出标注请求，待专家将数据集C标注完成后加入到K集合中，进行下一次迭代。  
按wiki上所描述的看，主动学习也属于半监督学习的范畴了，但实际上是不一样的，***半监督学习和直推学习(transductive learning)以及主动学习，都属于利用未标记数据的学习技术，但基本思想还是有区别的***。  

如上所述，主动学习的“主动”，指的是**主动提出标注请求，也就是说，还是需要一个外在的能够对其请求进行标注的实体(通常就是相关领域人员)，即主动学习是交互进行的**。

**而半监督学习，特指的是学习算法不需要人工的干预，基于自身对未标记数据加以利用**。

**直推学习，它与半监督学习一样不需要人工干预，不同的是，直推学习假设未标记的数据就是最终要用来测试的数据，学习的目的就是在这些数据上取得最佳泛化能力**。相对应的，半监督学习在学习时并不知道最终的测试用例是什么。
也就是说，直推学习其实类似于半监督学习的一个子问题，或者说是一个特殊化的半监督学习，所以也有人将其归为半监督学习。

&nbsp;
### 迁移学习
在传统的机器学习的框架下，学习的任务就是在给定充分训练数据的基础上来学习一个分类模型；然后利用这个学习到的模型来对测试文档进行分类与预测。  
然而，我们看到机器学习算法在当前的Web挖掘研究中存在着一个关键的问题：一些新出现的领域中的大量训练数据非常难得到。我们看到Web应用领域的发展非常快速。大量新的领域不断涌现，从传统的新闻，到网页，到图片,再到博客、播客等等。传统的机器学习需要对每个领域都标定大量训练数据，这将会耗费大量的人力与物力。而没有大量的标注数据，会使得很多与学习相关研究与应用无法开展。  
其次，传统的机器学习假设训练数据与测试数据服从相同的数据分布。然而，在许多情况下，这种同分布假设并不满足。通常可能发生的情况如训练数据过期。这往往需要我们去重新标注大量的训练数据以满足我们训练的需要，但标注新数据是非常昂贵的，需要大量的人力与物力。  
从另外一个角度上看，如果我们有了大量的、在不同分布下的训练数据，完全丢弃这些数据也是非常浪费的。  

如何合理的利用这些数据就是迁移学习主要解决的问题。迁移学习可以从现有的数据中迁移知识，用来帮助将来的学习。  
迁移学习（Transfer Learning）的目标就是**将从一个环境中学到的知识用来帮助新环境中的学习任务。因此，迁移学习不会像传统机器学习那样作同分布假设**。  举一个通俗的例子，一个会下象棋的人可以更容易的学会下围棋；一个认识桌子的人可以更加容易的认识椅子；

在迁移学习方面的工作目前可以分为以下三个部分：  
> 同构空间下基于实例的迁移学习;  
同构空间下基于特征的迁移学习;  
异构空间下的迁移学习。  

基于实例的迁移学习有更强的知识迁移能力，基于特征的迁移学习具有更广泛的知识迁移能力，而异构空间的迁移具有广泛的学习与扩展能力。

迁移学习即一种学习对另一种学习的影响，它广泛地存在于知识、技能、态度和行为规范的学习中。任何一种学习都要受到学习者已有知识经验、技能、态度等的影响，只要有学习，就有迁移。迁移是学习的继续和巩固，又是提高和深化学习的条件，学习与迁移不可分割。

&nbsp;

[在线学习on-line learning和增量学习Incremental Learning区别？ - Scofield的回答](https://www.zhihu.com/question/38713098/answer/161717769)

&nbsp;
## reference
[在线学习on-line learning和增量学习Incremental Learning区别？](https://www.zhihu.com/question/38713098)
