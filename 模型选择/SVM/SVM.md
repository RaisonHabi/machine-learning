## 0、支持向量
我们把这个划分数据的决策边界就叫做超平面。离这个超平面最近的点就是”支持向量”,   
点到超平面的距离叫做间隔，支持向量机的意思就是使超平面和支持向量之间的间隔尽可能的大，这样才可以使两类样本准确地分开。

[支持向量机（SVM）里的支持向量是什么意思](http://sofasofa.io/forum_main_post.php?postid=1000255)    
[什么是支持向量？](https://zhuanlan.zhihu.com/p/106265377)

&nbsp;
## 一、软间隔
在实际应用中，完全线性可分的样本是很少的，如果遇到了不能够完全线性可分的样本，我们应该怎么办？比如下面这个：

**于是我们就有了软间隔，相比于硬间隔的苛刻条件，我们允许个别样本点出现在间隔带里面**，比如：

&nbsp;
## 二、核函数/非线性 SVM
我们刚刚讨论的**硬间隔和软间隔都是在说样本的完全线性可分或者大部分样本点的线性可分**。

但我们可能会碰到的一种情况是样本点不是线性可分的，比如：

这种情况的解决方法就是：将二维线性不可分样本**映射到高维空间中，让样本点在高维空间线性可分**，比如：

**对于在有限维度向量空间中线性不可分的样本，我们将其映射到更高维度的向量空间里，再通过间隔最大化的方式，学习得到支持向量机，就是非线性 SVM**。

### 1、核函数的作用
我们不禁有个疑问：只是做个内积运算，为什么要有核函数的呢？

这是**因为低维空间映射到高维空间后维度可能会很大，如果将全部样本的点乘全部计算好，这样的计算量太大了**。

<p>但如果我们有这样的一核函数 <img src="https://www.zhihu.com/equation?tex=k%28x%2Cy%29+%3D+%28%5Cphi%28x%29%2C%5Cphi%28y%29%29" alt="k(x,y) = (\phi(x),\phi(y))" eeimg="1"/> ， <img src="https://www.zhihu.com/equation?tex=x_i" alt="x_i" eeimg="1"/> 与 <img src="https://www.zhihu.com/equation?tex=x_j" alt="x_j" eeimg="1"/> 在特征空间的内积等于它们在原始样本空间中通过函数 <img src="https://www.zhihu.com/equation?tex=k%28+x%2C+y%29" alt="k( x, y)" eeimg="1"/> 计算的结果，我们就不需要计算高维甚至无穷维空间的内积了。</p><p>举个例子：假设我们有一个多项式核函数：</p><p><img src="https://www.zhihu.com/equation?tex=k%28x%2Cy%29%3D%28x+%5Ccdot+y+%2B+1%29%5E2+%5C%5C" alt="k(x,y)=(x \cdot y + 1)^2 \\" eeimg="1"/> </p><p>带进样本点的后：</p><p><img src="https://www.zhihu.com/equation?tex=k%28x%2Cy%29+%3D+%28%5Csum_%7Bi%3D1%7D%5En%28x_i+%5Ccdot+y_i%29+%2B+1%29%5E2+%5C%5C" alt="k(x,y) = (\sum_{i=1}^n(x_i \cdot y_i) + 1)^2 \\" eeimg="1"/> </p><p>而它的展开项是：</p><p><img src="https://www.zhihu.com/equation?tex=%5Csum_%7Bi%3D1%7D%5Enx_i%5E2y_i%5E2%2B%5Csum_%7Bi%3D2%7D%5En%5Csum_%7Bj%3D1%7D%5E%7Bi-1%7D%28%5Csqrt2x_ix_j%29%28%5Csqrt2y_iy_j%29%2B%5Csum_%7Bi%3D1%7D%7Bn%7D%28%5Csqrt2x_i%29%28%5Csqrt2y_i%29%2B1+%5C%5C" alt="\sum_{i=1}^nx_i^2y_i^2+\sum_{i=2}^n\sum_{j=1}^{i-1}(\sqrt2x_ix_j)(\sqrt2y_iy_j)+\sum_{i=1}{n}(\sqrt2x_i)(\sqrt2y_i)+1 \\" eeimg="1"/> </p><p>如果没有核函数，我们则需要把向量映射成：</p><p><img src="https://www.zhihu.com/equation?tex=x%5E%7B%27%7D+%3D+%28x_1%5E2%2C...%2Cx_n%5E2%2C...%5Csqrt2x_1%2C...%2C%5Csqrt2x_n%2C1%29+%5C%5C" alt="x^{&#39;} = (x_1^2,...,x_n^2,...\sqrt2x_1,...,\sqrt2x_n,1) \\" eeimg="1"/> </p><p>然后在进行内积计算，才能与多项式核函数达到相同的效果。</p>

#### 可见核函数的引入一方面减少了我们计算量，另一方面也减少了我们存储数据的内存使用量。

### 2、常见核函数
<p>我们常用核函数有：</p><p><b>线性核函数</b></p><p><img src="https://www.zhihu.com/equation?tex=k%28x_i%2Cx_j%29+%3D+x_i%5ETx_j+%5C%5C" alt="k(x_i,x_j) = x_i^Tx_j \\" eeimg="1"/> </p><p><b>多项式核函数</b></p><p><img src="https://www.zhihu.com/equation?tex=+k%28x_i%2Cx_j%29+%3D+%28x_i%5ETx_j%29%5Ed%5C%5C" alt=" k(x_i,x_j) = (x_i^Tx_j)^d\\" eeimg="1"/> </p><p><b>高斯核函数</b></p><p><img src="https://www.zhihu.com/equation?tex=k%28x_i%2Cx_j%29+%3D+exp%28-%5Cfrac%7B%7C%7Cx_i-x_j%7C%7C%7D%7B2%5Cdelta%5E2%7D%29+%5C%5C" alt="k(x_i,x_j) = exp(-\frac{||x_i-x_j||}{2\delta^2}) \\" eeimg="1"/> </p><p>这三个常用的核函数中只有高斯核函数是需要调参的。</p>

&nbsp;
## 三、优缺点
### 1、优点
```
有严格的数学理论支持，可解释性强，不依靠统计方法，从而简化了通常的分类和回归问题；

能找出对任务至关重要的关键样本（即：支持向量）；

采用核技巧之后，可以处理非线性分类/回归任务；

最终决策函数只由少数的支持向量所确定，计算的复杂性取决于支持向量的数目，
而不是样本空间的维数，这在某种意义上避免了“维数灾难”。
```
### 2、缺点
```
训练时间长。当采用 SMO 算法时，由于每次都需要挑选一对参数，因此时间复杂度为 O(N2) ，其中 N 为训练样本的数量；

当采用核技巧时，如果需要存储核矩阵，则空间复杂度为 0(N2) ；

模型预测时，预测时间与支持向量的个数成正比。当支持向量的数量较大时，预测计算复杂度较高。
```
因此支持向量机目前只**适合小批量样本的任务，无法适应百万甚至上亿样本的任务**。

&nbsp;
## reference
[【机器学习】支持向量机 SVM（非常详细）](https://zhuanlan.zhihu.com/p/77750026)
