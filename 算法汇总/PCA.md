## 一、PCA
大概主成分分析（Principal components analysis，以下简称PCA）是最重要的降维方法之一。在数据压缩消除冗余和数据噪音消除等领域都有广泛的应用。一般我们提到降维最容易想到的算法就是PCA，下面我们就对PCA的原理做一个总结。

### 1.PCA最大可分性的思想
PCA顾名思义，就是找出数据里最主要的方面，用数据里最主要的方面来代替原始数据。具体的，假如我们的数据集是n维的，共有 m个数据(x1,x2,...,xm) 。
我们希望将这 m 个数据的维度从 n 维降到 n' 维，希望这 m 个 n' 维的数据集尽可能的代表原始数据集。我们知道数据从 n 维降到 n' 维肯定会有损失，但是我们希望损失尽可能的小。那么如何让这 n' 维的数据尽可能表示原来的数据呢？

我们先看看最简单的情况，也就是 n=2 ， n'=1,也就是将数据从二维降维到一维。数据如下图。我们希望找到某一个维度方向，它可以代表这两个维度的数据。
图中列了两个向量方向， u1和 u2 ，那么哪个向量可以更好的代表原始数据集呢？
从直观上也可以看出， u1 比 u2 好，这就是我们所说的最大可分性。

### 8.PCA算法总结
这里对PCA算法做一个总结。
作为一个非监督学习的降维方法，它只需要特征值分解，就可以对数据进行压缩，去噪。
因此在实际场景应用很广泛。为了克服PCA的一些缺点，出现了很多PCA的变种，比如为解决非线性降维的KPCA，还有解决内存限制的增量PCA方法Incremental PCA，以及解决稀疏数据降维的PCA方法Sparse PCA等。

PCA算法的主要优点有：
```
仅仅需要以方差衡量信息量，不受数据集以外的因素影响。　
各主成分之间正交，可消除原始数据成分间的相互影响的因素。
计算方法简单，主要运算是特征值分解，易于实现。
```
PCA算法的主要缺点有：
```
主成分各个特征维度的含义具有一定的模糊性，不如原始样本特征的解释性强。
方差小的非主成分也可能含有对样本差异的重要信息，因降维丢弃可能对后续数据处理有影响。
```
### 继续阅读
在PCA降维过程中，当进行协方差矩阵上求解特征值时，如果面对维度高达 10000*10000，可想而知耗费的计算量程平方级增长。面对这样一个难点，从而引出奇异值分解(SVD)，利用SVD不仅可以解出PCA的解，而且无需大的计算量。

PCA（主成分分析）和LDA（线性判别分析）有很多的相似点，其本质是要将初始样本映射到维度更低的样本空间中，  
但是PCA和LDA的**映射目标不一样：PCA是为了让映射后的样本具有最大的发散性；而LDA是为了让映射后的样本有最好的分类性能。所以说PCA是一种无监督的降维方法，而LDA是一种有监督的降维方法**。

&nbsp;

## 二、相关sklearn函数manifold.TSNE
### sklearn.manifold.TSNE
class sklearn.manifold.TSNE(n_components=2, *, perplexity=30.0, early_exaggeration=12.0, learning_rate=200.0, n_iter=1000, n_iter_without_progress=300, min_grad_norm=1e-07, metric='euclidean', init='random', verbose=0, random_state=None, method='barnes_hut', angle=0.5, n_jobs=None)
### init:	string or numpy array, default:”random”, 可以是’random’, ‘pca’或者一个numpy数组(shape=(n_samples, n_components)。

t-distributed Stochastic Neighbor Embedding.
### 数据降维与可视化——t-SNE
t-SNE是目前来说效果最好的数据降维与可视化方法，**但是它的缺点也很明显，比如：占内存大，运行时间长**。  
但是，当我们想要对高维数据进行分类，又不清楚这个数据集有没有很好的可分性（即同类之间间隔小，异类之间间隔大），可以通过t-SNE投影到2维或者3维的空间中观察一下。  
如果在低维空间中具有可分性，则数据是可分的；如果在高维空间中不具有可分性，可能是数据不可分，也可能仅仅是因为不能投影到低维空间。

#### t-distributed Stochastic Neighbor Embedding(t-SNE)
t-SNE（TSNE）将数据点之间的相似度转换为概率。原始空间中的相似度由高斯联合概率表示，嵌入空间的相似度由“学生t分布”表示。

虽然Isomap，LLE和variants等数据降维和可视化方法，更适合展开单个连续的低维的manifold。但如果要准确的可视化样本间的相似度关系，如对于下图所示的S曲线（不同颜色的图像表示不同类别的数据），t-SNE表现更好。因为t-SNE主要是关注数据的局部结构。


通过原始空间和嵌入空间的联合概率的Kullback-Leibler（KL）散度来评估可视化效果的好坏，也就是说用有关KL散度的函数作为loss函数，然后通过梯度下降最小化loss函数，最终获得收敛结果。注意，该loss不是凸函数，即具有不同初始值的多次运行将收敛于KL散度函数的局部最小值中，以致获得不同的结果。因此，尝试不同的随机数种子(Python中可以通过设置seed来获得不同的随机分布)有时候是有用的，并选择具有最低KL散度值的结果。

使用t-SNE的缺点大概是：
```
t-SNE的计算复杂度很高，在数百万个样本数据集中可能需要几个小时，而PCA可以在几秒钟或几分钟内完成
Barnes-Hut t-SNE方法（下面讲）限于二维或三维嵌入。
算法是随机的，具有不同种子的多次实验可以产生不同的结果。虽然选择loss最小的结果就行，但可能需要多次实验以选择超参数。
全局结构未明确保留。这个问题可以通过PCA初始化点（使用init ='pca'）来缓解。
```

&nbsp;
## 三、相关sklearn函数sklearn.manifold.MDS
class sklearn.manifold.MDS(n_components=2, *, metric=True, n_init=4, max_iter=300, verbose=0, eps=0.001, n_jobs=None, random_state=None, dissimilarity='euclidean')

Multidimensional scaling

示例：
```
>>> from sklearn.datasets import load_digits
>>> from sklearn.manifold import MDS
>>> X, _ = load_digits(return_X_y=True)
>>> X.shape
(1797, 64)
>>> embedding = MDS(n_components=2)
>>> X_transformed = embedding.fit_transform(X[:100])
>>> X_transformed.shape
(100, 2)
```

&nbsp;
## reference
[PCA主成分分析学习总结](https://zhuanlan.zhihu.com/p/32412043)  
[sklearn.manifold.TSNE](https://scikit-learn.org/stable/modules/generated/sklearn.manifold.TSNE.html)   
[数据降维与可视化——t-SNE](https://blog.csdn.net/hustqb/article/details/78144384)  
[sklearn.manifold.MDS](https://scikit-learn.org/stable/modules/generated/sklearn.manifold.MDS.html)
