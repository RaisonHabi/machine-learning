### 1.语言模型比较
```
tdidf、doc2vec稀疏，适合短文本
doc2vec效果时好时坏，偶然性大，不稳定
lsi、lda效果好且稳定，但lda计算量偏大
```
### 2.Doc2Vec 
或者叫做 paragraph2vec, sentence embeddings，是一种非监督式算法，可以获得 sentences/paragraphs/documents 的向量表达，是 word2vec 的拓展。

学出来的向量可以通过计算距离来找 sentences/paragraphs/documents 之间的相似性，可以用于文本聚类，对于有标签的数据，还可以用监督学习的方法进行文本分类，例如经典的情感分析问题。

**这个段落向量/句向量也可以认为是一个单词，它的作用相当于是上下文的记忆单元或者是这个段落的主题**，所以我们一般叫这种训练方法为Distributed Memory Model of Paragraph Vectors(PV-DM)

Distributed Bag of Words version of Paragraph Vector(PV-DBOW)
在上述两种方法中，我们可以使用PV-DM或者PV-DBOW得到段落向量/句向量。对于大多数任务，PV-DM的方法表现很好，但我们也强烈推荐两种方法相结合。

&nbsp;
## 语言模型
**语言模型：用来计算一个句子概率的模型。n-gram，通过最大似然函数估计构造语言模型**。
### 3.Word 2vec目标函数
NCE（noise contrastive estimation） _loss   
二分类交叉熵损失sigmoid _cross_entropy   
其他还有softmax_cross_entropy等   
### 4.Elmo、gpt、bret目标函数
[自然语言处理中的语言模型预训练方法（ELMo、GPT和BERT）](https://www.cnblogs.com/robert-dlut/p/9824346.html)
