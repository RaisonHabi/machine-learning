## LSI潜在语义索引/LSA潜在语义分析
在文本挖掘中，主题模型是比较特殊的一块，它的思想不同于我们常用的机器学习算法。
### 1、文本主题模型的问题特点
主题模型是非监督的算法，目的是得到文本按照主题的概率分布。从这个方面来说，主题模型和普通的聚类算法非常的类似。

但是两者其实还是有区别的：  
**a）聚类算法关注于从样本特征的相似度方面将数据聚类。比如通过数据样本之间的欧式距离，曼哈顿距离的大小聚类等**。  
b）而**主题模型，顾名思义，就是对文字中隐含主题的一种建模方法**。比如从“人民的名义”和“达康书记”这两个词我们很容易发现对应的文本有很大的主题相关度，但是**如果通过词特征来聚类的话则很难找出，因为聚类方法不能考虑到到隐含的主题这一块**。

那么如何找到隐含的主题呢？这个一个大问题。  
常用的方法一般都是基于统计学的生成方法。即假设以一定的概率选择了一个主题，然后以一定的概率选择当前主题的词。最后这些词组成了我们当前的文本。所有词的统计概率分布可以从语料库获得，具体如何以“一定的概率选择”，这就是各种具体的主题模型算法的任务了。

当然还有一些不是基于统计的方法，比如我们下面讲到的LSI。
### 2、潜在语义索引(LSI)概述
潜在语义索引(Latent Semantic Indexing,以下简称LSI)，有的文章也叫Latent Semantic  Analysis（LSA）。  
其实是一个东西，后面我们统称LSI，它是一种简单实用的主题模型。

**LSI是基于奇异值分解（SVD）的方法来得到文本的主题的**。

这里我们简要回顾下SVD：对于一个𝑚×𝑛的矩阵𝐴，可以分解为下面三个矩阵：𝐴𝑚×𝑛=𝑈𝑚×𝑚Σ𝑚×𝑛𝑉𝑇𝑛×𝑛   
有时**为了降低矩阵的维度到k，SVD的分解可以近似的写为：𝐴𝑚×𝑛≈𝑈𝑚×𝑘Σ𝑘×𝑘𝑉𝑇𝑘×𝑛**

SVD可以这样解释：我们输入的有m个文本，每个文本有n个词。而𝐴𝑖𝑗则对应第i个文本的第j个词的特征值，这里最常用的是基于预处理后的标准化TF-IDF值。k是我们假设的主题数，一般要比文本数少。SVD分解后，𝑈𝑖𝑙对应第i个文本和第l个主题的相关度。𝑉𝑗𝑚对应第j个词和第m个词义的相关度。Σ𝑙𝑚对应第l个主题和第m个词义的相关度。

也可以反过来解释：我们输入的有m个词，对应n个文本。而𝐴𝑖𝑗则对应第i个词档的第j个文本的特征值，这里最常用的是基于预处理后的标准化TF-IDF值。k是我们假设的主题数，一般要比文本数少。SVD分解后，𝑈𝑖𝑙对应第i个词和第l个词义的相关度。𝑉𝑗𝑚对应第j个文本和第m个主题的相关度。Σ𝑙𝑚对应第l个词义和第m个主题的相关度。

这样我们通过一次SVD，就可以得到文档和主题的相关度，词和词义的相关度以及词义和主题的相关度。
### 3、LSI主题模型总结
LSI是最早出现的主题模型了，它的算法原理很简单，一次奇异值分解就可以得到主题模型，同时解决词义的问题，非常漂亮。但是LSI有很多不足，导致它在当前实际的主题模型中已基本不再使用。

主要的问题有：  
```
1） SVD计算非常的耗时，尤其是我们的文本处理，词和文本数都是非常大的，对于这样的高维度矩阵做奇异值分解是非常难的。

2） 主题值的选取对结果的影响非常大，很难选择合适的k值。

3） LSI得到的不是一个概率模型，缺乏统计基础，结果难以直观的解释。
```
对于问题1），主题模型非负矩阵分解（NMF）可以解决矩阵分解的速度问题。  
对于问题2），这是老大难了，大部分主题模型的主题的个数选取一般都是凭经验的，较新的层次狄利克雷过程（HDP）可以自动选择主题个数。  
对于问题3），牛人们整出了pLSI(也叫pLSA)和隐含狄利克雷分布(LDA)这类基于概率分布的主题模型来替代基于矩阵分解的主题模型。

回到LSI本身，对于一些规模较小的问题，如果想快速粗粒度的找出一些主题分布的关系，则LSI是比较好的一个选择，其他时候，如果你需要使用主题模型，推荐使用LDA和HDP。

### 4、sklearn 的使用
[sklearn.decomposition.TruncatedSVD](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html)  

Dimensionality reduction using truncated SVD (aka LSA).

This transformer performs linear dimensionality reduction by means of truncated singular value decomposition (SVD). Contrary to PCA, this estimator does not center the data before computing the singular value decomposition. This means it can work with sparse matrices efficiently.

In particular, truncated SVD works on term count/tf-idf matrices as returned by the vectorizers in sklearn.feature_extraction.text. In that context, it is known as latent semantic analysis (LSA).

### 5、gensim 的使用
Gensim是一个开源库，使用现代统计机器学习来进行无监督的主题建模和自然语言处理。  
Gensim是用Python和Cython实现的。  
**Gensim旨在使用数据流和增量在线算法处理大型文本集合，这使其有别于仅针对内存中处理的大多数其他机器学习软件包**。 


[models.lsimodel – Latent Semantic Indexing](https://radimrehurek.com/gensim/models/lsimodel.html)

Examples
```
>>> from gensim.test.utils import common_dictionary, common_corpus
>>> from gensim.models import LsiModel
>>>
>>> model = LsiModel(common_corpus, id2word=common_dictionary)
>>> vectorized_corpus = model[common_corpus]  # vectorize input copus in BoW format
```

&nbsp;
## reference
[文本主题模型之潜在语义索引(LSI)](https://www.cnblogs.com/pinard/p/6805861.html)
