## 定义
归一化Google距离（Normalized Google Distance）是一种语义相似性的度量方法，由给定一组关键词集合的Google搜索引擎所返回的命中数量得出。

在自然语言中，**具有相同或相似意思的两个关键字在以归一化谷歌距离为单位的情况下趋向于“接近”，意思不同的两个关键字则趋向于“疏远”**。

<p><span style="font-size:18px;">具体地，两个搜索词x和y之间的归一化Google距离为：</span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20140525165317953" alt="" /><br /></span></p>
<p><span style="font-size:18px;">其中，M是Google搜索到的网页总数；f(x)和f(y)分别是搜索词x和y的命中数量；f(x,y)是同时出现x和y的网页数量。</span></p>

```
如果两个搜索词x和y从未一起出现在同一网页上，而是单独出现时，则他们之间的归一化Google距离是无穷。
如果两个词总是同时出现，则他们的归一化Google距离是0，或者相当于x平方和y平方之间的系数。
一般情况下，NGD(x,y)越接近于0，x、y越相似。
如果NGD(x,y)大于等于1，则x、y之间区别很大。
特殊情况下，由于搜索引擎结果不准确，NGD(x,y)可能会小于0。
```

&nbsp;
## References
[归一化Google距离（Normalized Google Distance）](https://blog.csdn.net/pinyangtenglong/article/details/26963015)  
[归一化谷歌距离的计算方法](https://zizhuowang.github.io/2016/12/09/%E5%BD%92%E4%B8%80%E5%8C%96%E8%B0%B7%E6%AD%8C%E8%B7%9D%E7%A6%BB%E7%9A%84%E8%AE%A1%E7%AE%97%E6%96%B9%E6%B3%95/)
