以前一直是按经验设置topic个数，数据量小就少点，数据量大就多点，大概50-500吧。最近有同学跟我讨论，才想起来该找找相关文献，学习一下如何科学的确定topic个数。
```
1.用perplexity-topic number曲线
2.用topic_number-logP(w|T)曲线，找到曲线中的纵轴最高点便是topic数量的最佳值。
3.计算topic之间的相似度
4. 利用HDP(层次狄利克雷过程)，自动确定主题个数。
```

Perplexity(困惑度)可以粗略的理解为“对于一篇文章，我们的LDA模型有多不确定它是属于某个topic 的”。topic越多，Perplexity越小，但是越容易overfitting。
我们利用Model Selection找到Perplexity又好，topic个数又少的topic数量。可以画出Perplexity vs  num of topics曲线，找到满足要求的点。
