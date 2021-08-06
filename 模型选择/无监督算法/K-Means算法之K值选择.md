## K值选择
### 1、拍脑袋法
一个非常快速的，拍脑袋的方法是将样本量除以2再开方出来的值作为K值
### 2、肘部法则（Elbow Method）
Elbow Method ：Elbow意思是手肘，如下图左所示，此种方法适用于 K 值相对较小的情况，当选择的k值小于真正的时，k每增加1，cost值就会大幅的减小；当选择的k值大于真正的K时， k每增加1，cost值的变化就不会那么明显。这样，正确的k值就会在这个转折点，类似elbow的地方。
```
# clustering dataset
# determine k using elbow method

from sklearn.cluster import KMeans
from scipy.spatial.distance import cdist
import numpy as np
import matplotlib.pyplot as plt

x1 = np.array([3, 1, 1, 2, 1, 6, 6, 6, 5, 6, 7, 8, 9, 8, 9, 9, 8])
x2 = np.array([5, 4, 5, 6, 5, 8, 6, 7, 6, 7, 1, 2, 1, 2, 3, 2, 3])

plt.plot()
plt.xlim([0, 10])
plt.ylim([0, 10])
plt.title('Dataset')
plt.scatter(x1, x2)
plt.show()

# create new plot and data
plt.plot()
X = np.array(list(zip(x1, x2))).reshape(len(x1), 2)
colors = ['b', 'g', 'r']
markers = ['o', 'v', 's']

# k means determine k
distortions = []
K = range(1, 10)
for k in K:
    kmeanModel = KMeans(n_clusters=k).fit(X)
    distortions.append(sum(np.min(cdist(X, kmeanModel.cluster_centers_, 'euclidean'), axis=1)) / X.shape[0])

# Plot the elbow
plt.plot(K, distortions, 'bx-')
plt.xlabel('k')
plt.ylabel('Distortion')
plt.title('The Elbow Method showing the optimal k')
plt.show()
```
### 3、间隔统计量 Gap Statistic
根据肘部法则选择最合适的K值有事并不是那么清晰，因此斯坦福大学的Robert等教授提出了Gap Statistic方法。

这里我们要继续使用上面的Dk。Gap Statistic的定义为:

Gapn(k)=En*(log(Dk))−logDk
### 4、轮廓系数（Silhouette Coefficient）
Silhouette method 会衡量对象和所属簇之间的相似度——即内聚性（cohesion）。当把它与其他簇做比较，就称为分离性（separation）。该对比通过 silhouette 值来实现，后者在 [-1, 1] 范围内。Silhouette 值接近 1，说明对象与所属簇之间有密切联系；反之则接近 -1。若某模型中的一个数据簇，生成的基本是比较高的 silhouette 值，说明该模型是合适、可接受的。
### 5、Canopy算法
肘部法则（Elbow Method）和轮廓系数（Silhouette Coefficient）来对k值进行最终的确定，但是这些方法都是属于“事后”判断的，而Canopy算法的作用就在于它是通过事先粗聚类的方式，为k-means算法确定初始聚类中心个数和聚类中心点。

与传统的聚类算法(比如K-Means)不同，Canopy聚类最大的特点是不需要事先指定k值(即clustering的个数)，因此具有很大的实际应用价值。与其他聚类算法相比，Canopy聚类虽然精度较低，但其在速度上有很大优势，因此可以使用Canopy聚类先对数据进行“粗”聚类，得到k值，以及大致的k个中心点，再使用K-Means进行进一步“细”聚类。所以Canopy+K-Means这种形式聚类算法聚类效果良好。


&nbsp;
## References
[K-Means算法之K值的选择](https://www.biaodianfu.com/k-means-choose-k.html)
