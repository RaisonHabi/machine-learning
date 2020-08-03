## EM算法存在的意义是什么？
对于含有隐变量的问题，直接使用最大似然估计(MLE)似乎也是可行的。MLE方法需要解一个非线性方程组，虽然难以求得最优解，但EM算法也很难求得最优解呀！是因为EM算法收敛速度更快，还是什么原因，使得EM算法这么流行呢？

EM算法理解的九层境界
```
EM 就是 E + M
EM 是一种局部下限构造
K-Means是一种Hard EM算法
从EM 到 广义EM
广义EM的一个特例是VBEM
广义EM的另一个特例是WS算法
广义EM的再一个特例是Gibbs抽样算法
WS算法是VAE和GAN组合的简化版
KL距离的统一
```
#### 第一层境界， EM算法就是E 期望 + M 最大化

#### 第二层境界， EM算法就一种局部下限构造
如果你再深入到基于隐变量的EM算法的收敛性证明， 基于log(x)函数的Jensen不等式构造， 我们很容易证明，EM算法是在反复的构造新的下限，然后进一步求解。  
<img src="https://pic1.zhimg.com/50/v2-a38b6748f36f0cb9bcd43b5ca435e5c6_hd.jpg?source=1940ef5c" data-caption="" data-size="small" data-rawwidth="788" data-rawheight="880" class="origin_image zh-lightbox-thumb" width="788" data-original="https://pic4.zhimg.com/v2-a38b6748f36f0cb9bcd43b5ca435e5c6_r.jpg?source=1940ef5c"/>

所以，先固定当前参数， 计算得到当前隐变量分布的一个下届函数， 然后优化这个函数， 得到新的参数， 然后循环继续。
#### 第三层境界， K-均值方法是一种Hard EM算法
在第二层境界的基础上， 你就能随意傲游EM算法用到GMM和HMM模型中去了。 尤其是对GMM的深入理解之后， 对于有隐变量的联合概率，如果利用高斯分布代入之后：  
<img src="https://pic2.zhimg.com/50/v2-0dcd2fc1da5497f561f0f7f5af175c9c_hd.jpg?source=1940ef5c" data-caption="" data-size="small" data-rawwidth="692" data-rawheight="204" class="origin_image zh-lightbox-thumb" width="692" data-original="https://pic3.zhimg.com/v2-0dcd2fc1da5497f561f0f7f5af175c9c_r.jpg?source=1940ef5c"/>

很容易就和均方距离建立联系：  
<img src="https://pic3.zhimg.com/50/v2-0e237f8a914a732b87599a0dea0c8367_hd.jpg?source=1940ef5c" data-caption="" data-size="small" data-rawwidth="728" data-rawheight="118" class="origin_image zh-lightbox-thumb" width="728" data-original="https://pic4.zhimg.com/v2-0e237f8a914a732b87599a0dea0c8367_r.jpg?source=1940ef5c"/>

但是，能不能说K-均值就是高斯分布的EM算法呢？不是， 这里虽然拓展到了相同的距离公式， 但是背后逻辑还是不一样， 不一样在哪里呢？  
**K-均值在讨论隐变量的决定时候，用的是dirac delta 分布， 这个分布是高斯分布的一种极限**。  
如果你觉得这个扩展不太好理解， 那么**更为简单直观的就是， k-均值用的hard EM算法， 而我们说的EM算法是soft EM算法**。    
**所谓hard 就是要么是，要么不是0-1抉择。 而Soft是0.7比例是c1，0.3比例是c2的情况**。

#### 第四层境界，EM 是 广义EM的特例
通过前3层境界， 你对EM算法的理解要跨过隐变量， 进入隐分布的境界。 如果我们把前面的EM收敛证明稍微重复一下，但是引入隐分布。

那么E步骤是固定参数优化隐分布， M步骤是固定隐分布优化参数，这就是广义EM算法了。

&nbsp;
## reference
[EM算法存在的意义是什么？ - 史博的回答 - 知乎](https://www.zhihu.com/question/40797593/answer/275171156)
