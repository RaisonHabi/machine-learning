## 一、简介
矩阵分解（Matrix Factorization，MF）是推荐系统中非常经典的一个算法，虽然现今工业界直接使用的较少，但其背后蕴含的编码降维思想，得以广泛应用于推荐领域之中。本文则主要来梳理下MF和FM的原理，灵感主要来源于大神张俊林的文章(张俊林：推荐系统召回四模型之：全能的FM模型)。

&nbsp;
## 二、MF（Matrix Factorization）
矩阵分解MF算法，同时兼具了协同过滤，隐语义分析和有监督学习的特性，加上矩阵分解的易实现性和高拓展性，成为推荐领域非常经典的算法。我们先来梳理下矩阵分解的基础算法。
### 1. SVD（奇异值分解）
说到矩阵分解，往往最先想到的是解析几何中奇异值分解这一概念。实际上，推荐系统中最初的矩阵分解算法也正是源于此。我们可以对User-Item矩阵A（存储用户对物品的评分）直接进行矩阵分解：
<figure data-size="normal"><noscript><img src="https://picb.zhimg.com/v2-1767891c87253368af47feb811fcc0c3_b.jpg" data-rawwidth="454" data-rawheight="86" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb" width="454" data-original="https://picb.zhimg.com/v2-1767891c87253368af47feb811fcc0c3_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;454&#39; height=&#39;86&#39;&gt;&lt;/svg&gt;" data-rawwidth="454" data-rawheight="86" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb lazy" width="454" data-original="https://picb.zhimg.com/v2-1767891c87253368af47feb811fcc0c3_r.jpg" data-actualsrc="https://picb.zhimg.com/v2-1767891c87253368af47feb811fcc0c3_b.jpg"/></figure>

然后通过选取Σ中前k大的奇异值，就可以对原先较大的矩阵A进行压缩。当我们要预测用户对物品的评分时，便可以直接从矩阵A中相对应的元素得到。

**这种简单的思路，在实际运用中往往存在两个致命缺陷**：
```
(1) 奇异值分解要求矩阵A是稠密的，也就是说不允许存在缺失值。而现实世界的User-Item矩阵却是存在大量空白的。

(2) 对M*N的矩阵进行奇异值分解的时间复杂度大约为O（N^3），这种计算量在大规模推荐场景下是无法接受的。
```
因此，必须对这种SVD方法进行改进才能运用到真实推荐场景中。回顾下上述的奇异值分解方法，本质上是在平方损失下对矩阵的最优近似：
<figure data-size="normal"><noscript><img src="https://pic1.zhimg.com/v2-863c9d4920063c3e513077d47f28585e_b.jpg" data-rawwidth="589" data-rawheight="227" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb" width="589" data-original="https://pic1.zhimg.com/v2-863c9d4920063c3e513077d47f28585e_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;589&#39; height=&#39;227&#39;&gt;&lt;/svg&gt;" data-rawwidth="589" data-rawheight="227" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb lazy" width="589" data-original="https://pic1.zhimg.com/v2-863c9d4920063c3e513077d47f28585e_r.jpg" data-actualsrc="https://pic1.zhimg.com/v2-863c9d4920063c3e513077d47f28585e_b.jpg"/></figure>

但由于A矩阵稀疏，无法直接得到精确解析解。那么我们能否通过改变表达式和求解方式来得到评分矩阵呢？

### 2. FunkSVD
2006年Netflix Prize开始后，Simon Funk在他的博客上提出了FunkSVD算法，一下子引爆了整个学术界对矩阵分解的关注。这种矩阵分解算法也被后来在Netflix Prize中夺冠的Koren称之为LFM（隐语义模型）。

**Simon Funk的思想很简单，将评分矩阵A只分解为两个K维矩阵P和Q（相当于把奇异值矩阵融入了P或Q中），同时忽略A中的缺失值，这样最小化平方损失可以表示为**：
<figure data-size="normal"><noscript><img src="https://pic1.zhimg.com/v2-803591fc261007d811d2d732c4410310_b.jpg" data-rawwidth="649" data-rawheight="599" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb" width="649" data-original="https://pic1.zhimg.com/v2-803591fc261007d811d2d732c4410310_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;649&#39; height=&#39;599&#39;&gt;&lt;/svg&gt;" data-rawwidth="649" data-rawheight="599" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb lazy" width="649" data-original="https://pic1.zhimg.com/v2-803591fc261007d811d2d732c4410310_r.jpg" data-actualsrc="https://pic1.zhimg.com/v2-803591fc261007d811d2d732c4410310_b.jpg"/></figure>

经上述转换，矩阵分解问题已经转变为了一个经典机器学习问题。一样可以在损失函数后面加上正则项，通过梯度下降法进行求解：
<figure data-size="normal"><noscript><img src="https://pic3.zhimg.com/v2-99029f8ecd14a95fbace4198bcbc5bcc_b.jpg" data-rawwidth="915" data-rawheight="433" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb" width="915" data-original="https://pic3.zhimg.com/v2-99029f8ecd14a95fbace4198bcbc5bcc_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;915&#39; height=&#39;433&#39;&gt;&lt;/svg&gt;" data-rawwidth="915" data-rawheight="433" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb lazy" width="915" data-original="https://pic3.zhimg.com/v2-99029f8ecd14a95fbace4198bcbc5bcc_r.jpg" data-actualsrc="https://pic3.zhimg.com/v2-99029f8ecd14a95fbace4198bcbc5bcc_b.jpg"/></figure>

这种方法相当于把用户、物品映射到了K维空间中，最终的评分矩阵就有K个隐因子决定：
<figure data-size="normal"><noscript><img src="https://pic4.zhimg.com/v2-070635afdd9bee43f33513c84c283750_b.png" data-rawwidth="787" data-rawheight="89" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb" width="787" data-original="https://pic4.zhimg.com/v2-070635afdd9bee43f33513c84c283750_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;787&#39; height=&#39;89&#39;&gt;&lt;/svg&gt;" data-rawwidth="787" data-rawheight="89" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb lazy" width="787" data-original="https://pic4.zhimg.com/v2-070635afdd9bee43f33513c84c283750_r.jpg" data-actualsrc="https://pic4.zhimg.com/v2-070635afdd9bee43f33513c84c283750_b.png"/></figure>

**这样对于未出现过的评分，我们也可以通过相应的用户和物品的隐向量计算得到，极大地提高了模型的泛化能力**。

虽然这样分解得到的各维度向量不具有正交的性质，但用于表达用户和物品特性已经足够了，并且可以运用机器学习理论保证在未知评分评估上的精确性。

&nbsp;
## 三、FM（Factorization Machine）
纵观推荐领域的发展历程，矩阵分解无疑在其中扮演了重要的角色。**但真实世界中的推荐框架，却较少地使用矩阵分解的方法**。   
```
一来是因为矩阵分解方法只适用于评分预估的类似场景中，在ctr预估等其它场景中较难逼近实际目标。
二来矩阵使用隐向量来表示用户和物品的特性，最终只能推荐训练样本中存在的用户和样本，冷启动问题较为严重。
```
**工业界常用的思路是将推荐问题转换为回归问题，进行ctr预估。其中逻辑回归（LR）是早期最成熟的模型。这种通过线性模型加上人工特征组合引入非线性的模式，有着模型简单可解释性强适用于大规模数据的优点。但LR模型最大的劣势也就在于人工组合特征，需要耗费大量时间和人力，有什么改善的方法呢**？

一个朴素的想法就是在LR模型中引入二阶交叉特征：
<figure data-size="normal"><noscript><img src="https://pic3.zhimg.com/v2-0f157a99d257305213ca08d77c7a3383_b.jpg" data-rawwidth="645" data-rawheight="119" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb" width="645" data-original="https://pic3.zhimg.com/v2-0f157a99d257305213ca08d77c7a3383_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;645&#39; height=&#39;119&#39;&gt;&lt;/svg&gt;" data-rawwidth="645" data-rawheight="119" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb lazy" width="645" data-original="https://pic3.zhimg.com/v2-0f157a99d257305213ca08d77c7a3383_r.jpg" data-actualsrc="https://pic3.zhimg.com/v2-0f157a99d257305213ca08d77c7a3383_b.jpg"/></figure>

相当于SVM采用了多项式核函数：
<figure data-size="normal"><noscript><img src="https://picb.zhimg.com/v2-cfc17ebbebb0cdd1f2c230b97d326001_b.jpg" data-rawwidth="1014" data-rawheight="440" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb" width="1014" data-original="https://picb.zhimg.com/v2-cfc17ebbebb0cdd1f2c230b97d326001_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;1014&#39; height=&#39;440&#39;&gt;&lt;/svg&gt;" data-rawwidth="1014" data-rawheight="440" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb lazy" width="1014" data-original="https://picb.zhimg.com/v2-cfc17ebbebb0cdd1f2c230b97d326001_r.jpg" data-actualsrc="https://picb.zhimg.com/v2-cfc17ebbebb0cdd1f2c230b97d326001_b.jpg"/></figure>

**这种方法虽然初步解决了特征两两组合的问题，但对于样本中未出现的交叉特征，无法对其参数进行估计，模型泛化能力差。这在大规模稀疏场景下是无法接受的，还需要进一步优化模型**。

此时，回顾下上章中介绍的矩阵分解法，同样也遇到过特征稀疏的问题，最终是通过引入K维隐向量提高了用户和物品的表达性来解决。  
**FM模型也采用了相同的思路解决二阶特性稀疏的问题，对于每个一维特征x赋予一个相对应的隐向量v，交叉特征的权重则由相对应的两个隐向量v的内积表示**。  

### 本质上都是对特征进行embedding化表征，将0/1这种二值硬匹配转换为向量软匹配，使得样本中未出现的值也能通过向量计算出来，具备良好的泛化性。

FM模型可以表述为：
<figure data-size="normal"><noscript><img src="https://pic2.zhimg.com/v2-3d36c6f29e42be1d510866a65a467ba2_b.jpg" data-rawwidth="761" data-rawheight="222" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb" width="761" data-original="https://pic2.zhimg.com/v2-3d36c6f29e42be1d510866a65a467ba2_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;761&#39; height=&#39;222&#39;&gt;&lt;/svg&gt;" data-rawwidth="761" data-rawheight="222" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb lazy" width="761" data-original="https://pic2.zhimg.com/v2-3d36c6f29e42be1d510866a65a467ba2_r.jpg" data-actualsrc="https://pic2.zhimg.com/v2-3d36c6f29e42be1d510866a65a467ba2_b.jpg"/></figure>

仔细留意下上述公式的特征交叉项，如果FM模型只取用户ID和物品ID作为特征，则交叉项中已经包含了前面提到FunkSVD算法的所有东西。并且FM模型在此基础上可以方便地引入更多特征，模型拓展性强。因此FM模型比矩阵分解更灵活，能适用于更多场景中。

最后再来看看计算效率的问题，相较于LR模型，FM加入二次项，计算的时间复杂度也相应地从O(n)提升到了O(n*n)。我们能否将FM的计算复杂度也降成线性的呢？

通过将FM公式中的二次项进行改写可以得到：
<figure data-size="normal"><noscript><img src="https://pic2.zhimg.com/v2-5c5f7b17913ab5efdb9ba72e38c66d9c_b.jpg" data-rawwidth="710" data-rawheight="631" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb" width="710" data-original="https://pic2.zhimg.com/v2-5c5f7b17913ab5efdb9ba72e38c66d9c_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;710&#39; height=&#39;631&#39;&gt;&lt;/svg&gt;" data-rawwidth="710" data-rawheight="631" data-size="normal" data-caption="" class="origin_image zh-lightbox-thumb lazy" width="710" data-original="https://pic2.zhimg.com/v2-5c5f7b17913ab5efdb9ba72e38c66d9c_r.jpg" data-actualsrc="https://pic2.zhimg.com/v2-5c5f7b17913ab5efdb9ba72e38c66d9c_b.jpg"/></figure>

从上式最后可以看出，**在计算FM二次项时，时间复杂度只需要O(k*n)是线性级别的。并且大规模推荐中特征往往是极为稀疏的，也就是说大量特征取值为0，因此FM计算效率可以进一步提高。这也是FM模型得以广泛地替代LR模型的一个关键所在**。

经过上述讨论，可以看出**FM模型能同时模拟二阶多项式核的SVM模型、MF模型和LR模型，并且拥有非常好的计算效率，因此在工业界中得以广泛的应用**。


&nbsp;
## References
[算法小板报(六)——初探MF(矩阵分解)和FM模型](https://zhuanlan.zhihu.com/p/68569485)   
[推荐系统召回四模型之：全能的FM模型](https://zhuanlan.zhihu.com/p/58160982)
