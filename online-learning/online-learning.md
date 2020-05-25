## 
### 在线学习与离线学习 
**offline learning and online learning**     
> In offline learning, **the whole training data must be available at the time of model training**. Only when training is completed can the model be used for predicting.   
	In contrast, online algorithms process data sequentially. They produce a model and put it in operation without having the complete training dataset available at the beginning. **The model is continuously updated during operation as more training data arrives**.

### 增量学习与减量学习
**online learning包括了incremental learning和decremental learning**  

incremental learning增量学习，是指一个学习系统能不断地从新样本中学习新的知识,并能保存大部分以前已经学习到的知识。增量学习非常类似于人类自身的学习模式。因为人在成长过程中,每天学习和接收新的事物,学习是逐步进行的,而且,对已经学习到的知识,人类一般是不会遗忘的。Less restrictive than online algorithms are incremental algorithms that process input examples one by one (or batch by batch) and update the decision model after receiving each example. Incremental algorithms may have random access to previous examples or representative/selected examples. In such a case, these algorithms are called in- cremental algorithms with partial memory. Typically, in incremental algorithms, for any new presentation of data, the update operation of the model is based on the previous one. Streaming algorithms are online algorithms for processing high-speed continuous flows of data. In streaming, examples are processed sequentially as well and can be examined in only a few passes (typically just one). These algorithms use limited memory and limited processing time per item.
```
一个增量学习算法应同时具有以下特点:
    1)可以从新数据中学习新知识;  
    2)以前已经处理过的数据不需要重复处理;
    3)每次只有一个训练观测样本被看到和学习;
    4)学习新知识的同时能保存以前学习到的大部分知识;
    5)—旦学习完成后训练观测样本被丢弃;
    6)学习系统没有关于整个训练样本的先验知识;
	增量式算法：就是每当新增数据时，并不需要重建所有的知识库，而是在原有知识库的基础上，仅做由于新增数据所引起的更新，这更加符合人的思维原理。
```
decremental learning递减学习，即抛弃“价值最低”的保留的训练样本。这两个概念在incremental and decremental svm这篇论文里面可以看到具体的操作过程。


[在线学习on-line learning和增量学习Incremental Learning区别？ - Scofield的回答](https://www.zhihu.com/question/38713098/answer/161717769)
&nbsp;
## reference
[在线学习on-line learning和增量学习Incremental Learning区别？](https://www.zhihu.com/question/38713098)
