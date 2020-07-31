## SVD
### 应用：推荐系统 (Recommender system)，可以说是最有价值的应用点；隐性语义分析 (Latent Semantic Analysis, LSA) 或隐性语义索引 (Latent Semantic Indexing, LSI)；矩阵形式数据（主要是图像数据）的压缩。

&nbsp;
### 0.问题
假设样本集 X=(x1,x2,...,xm) 中每个 xi 是一个图片，并且是一个 100 * 100 的图片，如果以像素值作为特征，那么每张图片的特征维度是10000。  
当进行PCA降维时，难点在于我们构造协方差矩阵时，维度达到 10000 * 10000 。在这样的协方差矩阵上求解特征值，耗费的计算量程平方级增长。  
面对这样一个难点，从而引出奇异值分解(SVD)，利用SVD不仅可以解出PCA的解，而且无需大的计算量。

&nbsp;
### 1.SVD的一些性质　
[奇异值分解(SVD)原理与在降维中的应用](https://www.cnblogs.com/pinard/p/6251584.html)  
上面几节我们对SVD的定义和计算做了详细的描述，似乎看不出我们费这么大的力气做SVD有什么好处。那么SVD有什么重要的性质值得我们注意呢？

**对于奇异值,它跟我们特征分解中的特征值类似，在奇异值矩阵中也是按照从大到小排列，而且奇异值的减少特别的快，在很多情况下，前10%甚至1%的奇异值的和就占了全部的奇异值之和的99%以上的比例**。  
也就是说，我们也可以用最大的k个的奇异值和对应的左右奇异向量来近似描述矩阵。也就是说：
**（公式详见参考，SVD计算举例、SVD的一些性质等过程解释了近似相等）**

#### 相似问题：奇异值分解的降维处理体现在哪里呢？
从上述定义可以看出，奇异值分解得到的三组件并没有体现出降维过程（dimensionality reduction），如果给定一个大小为 20000 * 10000 的矩阵（已经很大了），我们会发现通过奇异值分解得到三个矩阵大小依次是：[公式]，[公式]，[公式]，此时，分解出来的三组件更大了，并且加重了计算机的存储负担，我们不禁会问：奇异值分解的降维处理体现在哪里呢？

&nbsp;
### 2.小结
SVD作为一个很基本的算法，在很多机器学习算法中都有它的身影，特别是在现在的大数据时代，由于SVD可以实现并行化，因此更是大展身手。  
当然，SVD的缺点是分解出的矩阵解释性往往不强，不过这不影响它的使用。
```
SVD可以用于PCA降维，来做数据压缩和去噪。  

也可以用于推荐算法，将用户和喜好对应的矩阵做特征分解，进而得到隐含的用户需求来做推荐:
SVD另一个应用为推荐系统应用，简单版本的推荐系统能够计算物品item或者用户user之间的相似度，
可以用SVD将原始数据映射到低维空间中，然后节省计算相似度时的计算资源。

同时也可以用于NLP中的算法，比如潜在语义索引（LSI）。
SVD最早的应用之一是信息检索，我们称利用SVD的方法为潜在语义索引(Latent Semantic Indexing ，LSI)
或潜在语义分析(Latent Semantic Analysis ，LSA)。
```

&nbsp;
### 3.代码示例
[SVD-矩阵奇异值分解 —— 原理与几何意义](https://zhuanlan.zhihu.com/p/36546367)  
以下是基于 python 的 numpy 库实现的 SVD 矩阵分解的代码，用于实现图像压缩的功能。
```
# -*- coding: utf-8 -*-

import numpy as np
import numpy.linalg as la
import matplotlib.pyplot as plt
from sklearn import datasets
from skimage import io

def getImgAsMat(index):
    ds = datasets.fetch_olivetti_faces()
    return np.mat(ds.images[index])

def getImgAsMatFromFile(filename):
    img = io.imread(filename, as_grey=True)
    return np.mat(img) 

def plotImg(imgMat):
    plt.imshow(imgMat, cmap=plt.cm.gray)
    plt.show()

def recoverBySVD(imgMat, k):
    # singular value decomposition
    U, s, V = la.svd(imgMat)
    # choose top k important singular values (or eigens)
    Uk = U[:, 0:k]
    Sk = np.diag(s[0:k])
    Vk = V[0:k, :]
    # recover the image
    imgMat_new = Uk * Sk * Vk
    return imgMat_new


# -------------------- main --------------------- #
#A = getImgAsMat(0)
#plotImg(A)
#A_new = recoverBySVD(A, 20)
#plotImg(A_new)

A = getImgAsMatFromFile('D:/pic.jpg')
plotImg(A)
A_new = recoverBySVD(A, 30)
plotImg(A_new)
```
这里用小黄人的大头照做个例子看看：  
原图：  
设置 k = 10，得到的压缩图：  
k = 20：
k = 30：
继续增加 k 值，将会得到越来越接近原始图的压缩图。

&nbsp;
### 4.物理意义举例解释
[机器学习系列-SVD篇](https://zhuanlan.zhihu.com/p/25801478)  
用一个大矩阵A来描述这一百万篇文章和五十万词的关联性。这个矩阵中，每一行对应一篇文章，每一列对应一个词。 

在上面的图中，M=1,000,000，N=500,000。第 i 行，第 j 列的元素，是字典中第 j 个词在第 i 篇文章中出现的加权词频（比如，TF/IDF)。  
读者可能已经注意到了，这个矩阵非常大，有一百万乘以五十万，即五千亿个元素。

奇异值分解就是把上面这样一个大矩阵，近似分解成三个小矩阵相乘，如下图所示。

三个矩阵有非常清楚的物理含义：
```
第一个矩阵X中的每一行表示意思相关的一类词，其中的每个非零元素表示这类词中每个词的重要性（或者说相关性），数值越大越相关。

第三个矩阵Y中的每一列表示同一主题一类文章，其中每个元素表示这类文章中每篇文章的相关性。

第二个矩阵B则表示类词和文章之间的相关性。因此，我们只要对关联矩阵A进行一次奇异值分解，我们就可以同时完成了近义词分类和文章的分类。（同时得到每类文章和每类词的相关性）。
```
