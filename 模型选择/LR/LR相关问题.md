## 问题
### 1、为什么机器学习的分类器用logistic模型？
不是非得用 Logistic 函数，其它从 0 到 1 光滑单调递增的函数也可以用。  
但为什么都在用 Logistic 呢？简单地说，这**是由广义线性模型推导出来的函数**。

先确定下，这里的分类器为二分类器，如果是多分类器，概率分布就不一样了。  
**这里二分类器的假设是，给定 x 后，y 是符合伯努利分布的**。对于不同的 x 值，y 就符合不同均值的伯努利分布。

举一个邮件分类的例子，要将邮件分为垃圾邮件和非垃圾邮件，当检测到邮件中包含的字词时，即确定了 x，那么这邮件是垃圾的概率是多少，即 y 为 1 的概率是多少，这时 y 就是一个由 x 决定的伯努利分布。这个概率就是 y 的伯努利分布的均值。<br/>伯努利分布的唯一参数是 <img src="https://www.zhihu.com/equation?tex=%5Cphi+" alt="\phi " eeimg="1"/>，也是期望。<br/><br/>p(y;<img src="https://www.zhihu.com/equation?tex=%5Cphi+" alt="\phi " eeimg="1"/>)=<img src="https://www.zhihu.com/equation?tex=%5Cphi%5E%7By%7D+%281-%5Cphi%29%5E%7B1-y%7D" alt="\phi^{y} (1-\phi)^{1-y}" eeimg="1"/><br/><br/>说 x 决定 y 的伯努利分布，其实 x 就是通过 <img src="https://www.zhihu.com/equation?tex=%5Cphi+" alt="\phi " eeimg="1"/> 来决定 y 的分布的。如何把 x 映射到 <img src="https://www.zhihu.com/equation?tex=%5Cphi+" alt="\phi " eeimg="1"/>，什么样的 x 对应什么样的 <img src="https://www.zhihu.com/equation?tex=%5Cphi+" alt="\phi " eeimg="1"/>，这是需要通过训练集来学习的，学习到的参数记作 <img src="https://www.zhihu.com/equation?tex=%5Ctheta+" alt="\theta " eeimg="1"/>。那么，<img src="https://www.zhihu.com/equation?tex=%5Cphi+" alt="\phi " eeimg="1"/>、<img src="https://www.zhihu.com/equation?tex=%5Ctheta+" alt="\theta " eeimg="1"/> 和 x 的函数关系是什么呢？先给答案再解释。<br/><br/><img src="https://www.zhihu.com/equation?tex=%5Cphi+%3D%5Cfrac%7B1%7D%7B1%2Be%5E%7B-%5Ctheta+%5E%7BT%7D+x%7D+%7D+" alt="\phi =\frac{1}{1+e^{-\theta ^{T} x} } " eeimg="1"/><br/><br/>这就是所谓的 Logistic 模型。<br/>这个模型怎么来的？简单地说，就是套用广义线性模型得来的。<br/>（至于为什么伯努利模型要套用到广义线性模型中，是因为广义线性模型有许多优良的属性，如易学习性，而且广义线性模型使我们可以导出许多优雅的学习算法类，对建模许多不同的分布都非常有效，如多项式分布、泊松分布、伽马分布、指数分布、贝塔分布和 Dirichlet 分布等）<br/><br/>广义线性模型要求概率分布属于指数分布簇，伯努利模型属于指数分布簇，凡是概率能写成以下形式的都是指数分布簇。<br/><figure><noscript><img src="https://pic2.zhimg.com/50/f2c1e10381ab71689e57d374335ee918_hd.jpg?source=1940ef5c" data-rawwidth="341" data-rawheight="35" class="content_image" width="341"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;341&#39; height=&#39;35&#39;&gt;&lt;/svg&gt;" data-rawwidth="341" data-rawheight="35" class="content_image lazy" width="341" data-actualsrc="https://pic2.zhimg.com/50/f2c1e10381ab71689e57d374335ee918_hd.jpg?source=1940ef5c"/></figure>把上面的伯努利概率模型修改成这种形式，可得：<br/><figure><noscript><img src="https://pic3.zhimg.com/50/bffd3de4bf5d970c3d343c86eb35fa57_hd.jpg?source=1940ef5c" data-rawwidth="508" data-rawheight="128" class="origin_image zh-lightbox-thumb" width="508" data-original="https://pic1.zhimg.com/bffd3de4bf5d970c3d343c86eb35fa57_r.jpg?source=1940ef5c"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;508&#39; height=&#39;128&#39;&gt;&lt;/svg&gt;" data-rawwidth="508" data-rawheight="128" class="origin_image zh-lightbox-thumb lazy" width="508" data-original="https://pic1.zhimg.com/bffd3de4bf5d970c3d343c86eb35fa57_r.jpg?source=1940ef5c" data-actualsrc="https://pic3.zhimg.com/50/bffd3de4bf5d970c3d343c86eb35fa57_hd.jpg?source=1940ef5c"/></figure>其中：<br/><figure><noscript><img src="https://pic3.zhimg.com/50/44d304e8212afa77426685ecaeb9b908_hd.jpg?source=1940ef5c" data-rawwidth="194" data-rawheight="400" class="content_image" width="194"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;194&#39; height=&#39;400&#39;&gt;&lt;/svg&gt;" data-rawwidth="194" data-rawheight="400" class="content_image lazy" width="194" data-actualsrc="https://pic3.zhimg.com/50/44d304e8212afa77426685ecaeb9b908_hd.jpg?source=1940ef5c"/></figure>可以看到 <img src="https://www.zhihu.com/equation?tex=%5Cphi+" alt="\phi " eeimg="1"/> 的定义就是个 Logistic 函数了。<br/>在广义线性模型中，<img src="https://www.zhihu.com/equation?tex=%5Ceta+%3D%5Ctheta+%5E%7BT%7D+x" alt="\eta =\theta ^{T} x" eeimg="1"/>，可得前面 <img src="https://www.zhihu.com/equation?tex=%5Cphi+" alt="\phi " eeimg="1"/> 的 Logistic 公式。<br/><br/><img src="https://www.zhihu.com/equation?tex=%5Cphi+%3D%5Cfrac%7B1%7D%7B1%2Be%5E%7B-%5Ctheta+%5E%7BT%7D+x%7D+%7D+" alt="\phi =\frac{1}{1+e^{-\theta ^{T} x} } " eeimg="1"/><br/><br/>想深入了解 Logistic 模型，务必要了解广义线性模型。<br/><br/>参考资料：<br/>1、<a href="https://link.zhihu.com/?target=http%3A//cs229.stanford.edu/notes/cs229-notes1.pdf" class=" external" target="_blank" rel="nofollow noreferrer">
<span class="invisible">http://</span><span class="visible">cs229.stanford.edu/note</span><span class="invisible">s/cs229-notes1.pdf</span><span class="ellipsis"></span></a></span>

&nbsp;
### 2.共线性问题
线性回归模型的多重共线性问题：**共线性，指多元回归模型中，各自变量之中至少有两个完全或高度相关**。  

自变量之间的强相关，可能导致回归系数的正负方向与真实的相反，影响特征的可解释性。

可以认为 LR 来源于多元回归，将元与特征，自变量与特征，回归系数与权重一一对应就可以啦。  
示例：  
```
首先，将问题推向极端，假设存在两个线性相关的特征 x1 和 x2，有 y = w1x1 + w2x2 + others。
如果 x1 和 x2 分别代表速度特征 m/s 和 km/h，即 x2 = 3.6 * x1，则 y = (w1+3.6w2)x1 + others。
如果理论上速度和 y 的关系是 7 倍，即最优解 w1+3.6w2 = 7。来看看寻找这个最优解时会发生什么。
一方面，值更大的特征对应的 w2 的变动对 7 的影响要大于值更小的特征对应的 w1 的变动。
优化过程中，w2 的变化使得 w1 产生较大的变化，可能会出现，w1 变成负值的情况。
可是前面说过 y 和速度是正相关的，如此以来，x1 与 y 成了负相关，显然有悖现实。
当我们用权重来解释特征影响时，这会造成误解。
```

**多重共线性的特征不会对决策树、随机森林的预测能力有影响，但是数据的解释性会被多重共线性影响**       
随机森林可以返回特征的重要性（importantce），当有多重共线性时，importance会被影响。
```
比如说，特征A和B完全一样，我们在用随机森林时，它们的重要性应该非常接近（考虑到随机性）。
如果我们在训练前删除特征B，那么特征A的重要性就会翻番。这一下子就影响了我们对特征、数据的理解。
多重共线性最极端的情况是有两个完全一样的特征，特征A和特征B。
当特征A被使用之后，决策树不会再选择使用特征B，因为特征B并没有增加新的有效信息。
同理，如果决策树先选择了使用特征B，那么特征A也不会再被使用。 
所以基于树的模型不会受到多重共线性的影响。
```
造成多重共线性的原因：
```
1、解释变量都享有共同的时间趋势；
2、一个解释变量是另一个的滞后，二者往往遵循一个趋势；
3、由于数据收集的基础不够宽，某些解释变量可能会一起变动；
4、某些解释变量间存在某种近似的线性关系；
```
判别：
```
1、发现系数估计值的符号不对；
2、某些重要的解释变量t值低，而R方不低
3、当一不太重要的解释变量被删除后，回归结果显著变化；
```
检验：
```
1、相关性分析，相关系数高于0.8，表明存在多重共线性；但相关系数低，并不能表示不存在多重共线性；
2、vif检验；
3、条件系数检验；
```
解决方法：
```
1、增加数据；
2、对模型施加某些约束条件；
3、删除一个或几个共线变量；
4、将模型适当变形；
5、主成分回归
```
处理多重共线性的原则：
```
1、多重共线性是普遍存在的，轻微的多重共线性问题可不采取措施；

2、严重的多重共线性问题，一般可根据经验或通过分析回归结果发现。
如影响系数符号，重要的解释变量t值很低。要根据不同情况采取必要措施。

3、如果模型仅用于预测，则只要拟合程度好，可不处理多重共线性问题，存在多重共线性的模型用于预测时，往往不影响预测结果；
```

#### LR解决过拟合的方法：
```
增加数据量（万能办法）； 
减少特征：手动剔除；特征选择算法； 
正则化：结构风险最小化策略 
数据稀疏：L1正则化 
其他情况：L2正则化 
```
#### 共线性问题有如下几种检验方法： 
```
相关性分析。检验变量之间的相关系数； 
方差膨胀因子VIF。当VIF大于5或10时，代表模型存在严重的共线性问题； 
条件数检验。当条件数大于100、1000时，代表模型存在严重的共线性问题。 
```
#### 共线性解决办法： 
```
PCA等降维法； 
逐步回归法； 
岭回归、L2正则化； 
ElasticNet回归等 
ElasticNet回归同时兼顾了L1和L2惩罚项。 
```
多重共线性是使用线性回归算法时经常要面对的一个问题。 

在其他算法中，例如决策树和贝叶斯，前者的建模过程是逐步递进，每次拆分只有一个变量参与，这种建模机制含有抗多重共线性干扰的功能；后者干脆假定变量之间是相互独立的，因此从表面上看，也没有多重共线性的问题。 

但是对于回归算法，不论是一般回归，逻辑回归，或存活分析，都要同时考虑多个预测因子，因此多重共线性是不可避免需要面对的，在很多时候，多重共线性是一个普遍的现象。在构造预测模型时如何处理多重共线性是一个比较微妙的议题。既不能不加控制，又不能一刀切，认为凡是多重共线性就应该消除。 

&nbsp;
### 3.LR可以用来处理非线性问题么？（还是lr啊 只不过是加了核的lr 这里加核是显式地把特征映射到高维 然后再做lr）怎么做？可以像SVM那样么？为什么？
可以引入交叉项等作为显式的高维映射，类似特征组合，但是需要根据业务设定，**数据稀疏导致部分参数训练不够，可能影响模型性能**

使用FM模型，对于因子分解机FM来说，最大的特点是对于稀疏的数据具有很好的学习能力。

&nbsp;
### 4.参数模型与非参数模型
#### LR是参数模型，SVM是非参数模型。

参数模型、非参数模型（以及半参数模型）的概念应该源自于统计学中。  
统计专业中有一门课程叫做《非参数统计》，研究的对象就是秩检验、核密度估计等。

在统计学中，**参数模型通常假设总体（随机变量）服从某一个分布，该分布由一些参数确定（比如正太分布由均值和方差确定），在此基础上构建的模型称为参数模型**；  

**非参数模型对于总体的分布不做任何假设，只是知道总体是一个随机变量，其分布是存在的（分布中也可能存在参数），但是无法知道其分布的形式，更不知道分布的相关参数，只有在给定一些样本的条件下，能够依据非参数统计的方法进行推断**。
 
从上述的区别中可以看出，**问题中有没有参数，并不是参数模型和非参数模型的区别。其区别主要在于总体的分布形式是否已知**。  
而为何**强调“参数”与“非参数”，主要原因在于参数模型的分布可以有参数直接确定**

#### 什么是非参数（nonparametric）模型？
首先，任何一个模型（Model）的建立都有其基础或假设（Assumptions）。  
而参数模型（parametric models）和非参数（Nonparametric models）亦不例外：   
二者最主要的区别是关于数据分布的假设——参数模型对数据分布（distribution，density）有假设，  
而非参数模型对数据分布假设自由（distribution－free），但是对数据必须可以排序（rank，score）  
。所以，回顾二者的名字“参数”，即指数据分布的参数。
[能不能用简明的语言解释什么是非参数（nonparametric）模型？ - 爱文的回答 - 知乎](https://www.zhihu.com/question/22855599/answer/72807438)
&nbsp;
## reference
[为什么机器学习的分类器用logistic模型？ - 增益的回答 - 知乎](https://www.zhihu.com/question/24429347/answer/91824945)  
[逻辑斯蒂回归中特征共线性或强相关的影响](https://blog.csdn.net/lipengcn/article/details/82467082)    
[多重共线性的产生原因、判别、检验、解决方法](https://blog.csdn.net/diyiziran/article/details/17025471)   
[参数模型与非参数模型](https://blog.csdn.net/sinat_27652257/article/details/80543604)
