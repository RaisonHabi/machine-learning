### Elmo
该模型**不仅去学习单词特征，还有句法特征与语义特征**。后两者的特征是来自LSTM的隐含输出向量。  

模型训练完，我们就可以得到单词，句法以及语义特征。也就是在一个句子中每一个单词将会对应三个向量，然后三者共同构建成下游任务的输入。比如下游任务就是一个对话系统，整个流程如下图所示。

[经典算法·从ELMo、GPT到bert](https://zhuanlan.zhihu.com/p/54578457)

&nbsp;
### 比较
Elmo采用lstm 进行特征提取，gpt 和bert 采用transformer提取，  
transformer是encoder-decoder结构，gpt 采用decoder部分，见到句子不完整，bert 采用encoder部分，采用 句子完整；  
（双向transformer称为transformer encoder ；而左侧上下文被称为transformer decoder，不能获取要预测的信息）

Gpt 采用单向语言模型，elmo 和Bert 采用双向，但elmo实际是两个单向的LSTM的最高层进行简单的拼接。

Bert：masked LM、transformer、sentence-level

elmo的用法和word2vec差不多，只不过是用语言模型生成的动态vec再输入到原有的model（解决之前task的model）中去。   
### elmo下游任务：  
先将句子X作为预训练好的ELMo网络的输入，这样句子X中每个单词在 ELMo网络中都能获得对应的三个 Embedding，  
之后给予这三个 Embedding 中的每一个 Embedding 一个权重 a，这个权重可以学习得来，根据各自权重累加求和，将三个 Embedding 整合成一个。

然后将整合后的这个 Embedding 作为 X 句在自己任务的那个网络结构中对应单词的输入，以此作为补充的新特征给下游任务使用。
**因为 ELMO给下游提供的是每个单词的特征形式，所以这一类预训练的方法被称为“Feature-based Pre-Training”**。

### gpt下游任务（微调过程Fine-Tuning ）：
但是OpenAI GPT的supervised fine-tuning这个指的是直接用transformer fine-tuning之后的模型就能直接完成特定的task 而不是要像word2vec和elmo输入到之前的model中去。

对不同的下游任务来说，本来你可以任意设计自己的网络结构，现在不行了，你要向 GPT 的网络结构看齐，把任务的网络结构改造成和 GPT 的网络结构是一样的。
然后，在做下游任务的时候，利用第一步预训练好的参数初始化 GPT 的网络结构，这样通过预训练学到的语言学知识就被引入到你手头的任务里来了。

微调：实践中由于数据集不够大，很少有人从头开始训练网络，常见的做法是使用预训练的网络来重新微调(增加少量神经网络层完成特定任务，采用自己语料训练模型)

微调与elmo 当成特征的做法不同，不需要重新对任务构建新的模型结构，而是直接在transformer 这个语言模型上的最后一层接上softmax 作为任务输出层，然后再对整个模型微调。
