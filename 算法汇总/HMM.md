### 马尔可夫性质
概率论中的一个概念，因为俄国数学家安德雷·马尔可夫得名。  
当一个随机过程在给定现在状态及所有过去状态情况下，其未来状态的条件概率分布仅依赖于当前状态；  
换句话说，在给定现在状态时，它与过去状态是条件独立的，那么此随机过程即具有马尔可夫性质。   
具有马尔可夫性质的过程通常称之为马尔可夫过程。

&nbsp;
## 一、前言
隐马尔科夫模型（Hidden Markov Model，HMM），和回归、分类那些处理相互独立的样本数据的模型不同，它用于**处理时间序列数据，即样本之间有时间序列关系的数据**。  
从这一点来说，它和卡尔曼滤波算法很像。事实上，HMM和卡尔曼滤波的算法本质是一模一样的，只不过HMM要假设隐藏变量是离散的，而卡尔曼滤波假设隐藏变量是连续的。  
**隐藏变量**是HMM里的关键概念之一，可以理解为无法直接观测到的变量，即HMM中Hidden一词的含义；与之相对的是观测变量，即可以直接观测到的变量；  
HMM的能力在于能够根据给出的观测变量序列，估计对应的隐藏变量序列是什么，并对未来的观测变量做预测。

比如语音识别，给你一段音频数据，需要识别出该音频数据对应的文字。

除了语音识别，你可能已经想到了另一个与之相近的例子了，输入法。就拿中文拼音输入法来说，给你一段从键盘输入的字符，你需要从中推测出用户输入的文字是什么，如果有多种可能的文字，你甚至需要给出每段候选文字的概率是多少。

HMM的应用远不止于此，还有手写识别、地图匹配、金融预测、DNA序列分析等等。这里之所以啰嗦这么多，是想强调几点，这几点也是我们在判断一个问题是否适合用HMM解决的关键：
```
1. 我们已知一组数据，需要推断与之对应的另一组数据。比如音频数据、字符序列是已知数据，对应的文字是要推断的数据。

2. 要推断的数据是离散的。比如前面两个例子中的文字。而对观测数据没有要求，既可以是离散的，也可以是连续的。

3. 对输入数据的顺序敏感。比如音频数据和字符序列，如果把它们的顺序打乱，结果就完全不一样了。 对顺序敏感并不是坏事，顺序即信息，有信息才可以做推断。
```

&nbsp;
## 二、马尔科夫模型与HMM
### 1.马尔科夫模型
要讲隐马尔科夫模型，需要先从马尔科夫模型讲起。已知N个有序随机变量，根据贝叶斯定理，他们的联合分布可以写成条件分布的连乘积：
<p><img src="https://www.zhihu.com/equation?tex=p%28x_1%2Cx_2%2C%5Ccdots%2Cx_N%29+%3D+%5Cprod_%7Bn%3D1%7D%5E%7BN%7D%7Bp%28x_n%7Cx_%7Bn-1%7D%2C%5Ccdots%2Cx_1%29%7D" alt="p(x_1,x_2,\cdots,x_N) = \prod_{n=1}^{N}{p(x_n|x_{n-1},\cdots,x_1)}" eeimg="1"/>                (1)    </p>

**马尔科夫模型是指，假设序列中的任何一个随机变量在给定它的前一个变量时的分布与更早的变量无关**，即：
<img src="https://www.zhihu.com/equation?tex=p%28x_n%7Cx_%7Bn-1%7D%2C%5Ccdots%2Cx_1%29+%3D+p%28x_n%7Cx_%7Bn-1%7D%29" alt="p(x_n|x_{n-1},\cdots,x_1) = p(x_n|x_{n-1})" eeimg="1"/> 

在此假设下，N个随机变量的联合分布可以简化为：
<img src="https://www.zhihu.com/equation?tex=p%28x_1%2Cx_2%2C%5Ccdots%2Cx_N%29+%3D+p%28x_1%29+%5Cprod_%7Bn%3D2%7D%5EN%7Bp%28x_n%7Cx_%7Bn+-+1%7D%29%7D" alt="p(x_1,x_2,\cdots,x_N) = p(x_1) \prod_{n=2}^N{p(x_n|x_{n - 1})}" eeimg="1"/> 

一阶马尔科夫性只能表达当前变量与前一个变量的关系，然而很多实际问题往往没有这么简单。  
为了表达当前变量与更早的变量之间的关系，可以引入高阶马尔科夫性。概括来说，**M阶马尔科夫性是指当前随机变量在给定其之前的M个变量时与更早的变量无关**，用公式表达就是：
<img src="https://www.zhihu.com/equation?tex=p%28x_n%7Cx_%7Bn-1%7D%2C%5Ccdots%2Cx_1%29+%3D+p%28x_n%7Cx_%7Bn-1%7D%2C%5Ccdots%2Cx_%7Bn-M%7D%29" alt="p(x_n|x_{n-1},\cdots,x_1) = p(x_n|x_{n-1},\cdots,x_{n-M})" eeimg="1"/>  

<p>高阶马尔科夫性虽然达到了关联当前变量与更早的变量的目的，但有一个问题就是指数爆炸问题，即参数数量随着M的增大呈指数增长。假设每个随机变量有 <img src="https://www.zhihu.com/equation?tex=K" alt="K" eeimg="1"/> 种状态，对于一阶马尔科夫模型而言，要表达条件分布 <img src="https://www.zhihu.com/equation?tex=p%28x_n%7Cx_%7Bn-1%7D%29" alt="p(x_n|x_{n-1})" eeimg="1"/>，因为对于 <img src="https://www.zhihu.com/equation?tex=x_%7Bn-1%7D" alt="x_{n-1}" eeimg="1"/> 的每个取值（共有 <img src="https://www.zhihu.com/equation?tex=K" alt="K" eeimg="1"/> 个取值）需要 <img src="https://www.zhihu.com/equation?tex=K" alt="K" eeimg="1"/> 个 <img src="https://www.zhihu.com/equation?tex=x_n" alt="x_n" eeimg="1"/> 的取值，根据条件概率的定义，这 <img src="https://www.zhihu.com/equation?tex=K" alt="K" eeimg="1"/> 个值的和为1，因此实际有 <img src="https://www.zhihu.com/equation?tex=K-1" alt="K-1" eeimg="1"/> 个自由参数，因此共需要 <img src="https://www.zhihu.com/equation?tex=K%28K-1%29" alt="K(K-1)" eeimg="1"/> 个参数。同理，对于 <img src="https://www.zhihu.com/equation?tex=M" alt="M" eeimg="1"/> 阶马尔科夫模型而言，要表达条件分布 <img src="https://www.zhihu.com/equation?tex=+p%28x_n%7Cx_%7Bn-1%7D%2C%5Ccdots%2Cx_%7Bn-M%7D%29" alt=" p(x_n|x_{n-1},\cdots,x_{n-M})" eeimg="1"/> ，则需要 <img src="https://www.zhihu.com/equation?tex=K%5E%7BM%7D%28k-1%29" alt="K^{M}(k-1)" eeimg="1"/> 个参数（对于 <img src="https://www.zhihu.com/equation?tex=x_%7Bn-1%7D%2C%5Ccdots%2Cx_%7Bn-M%7D" alt="x_{n-1},\cdots,x_{n-M}" eeimg="1"/> 的每一个取值的组合（共 <img src="https://www.zhihu.com/equation?tex=K%5EM" alt="K^M" eeimg="1"/> 个），都需要 <img src="https://www.zhihu.com/equation?tex=K-1" alt="K-1" eeimg="1"/> 个 <img src="https://www.zhihu.com/equation?tex=x_n" alt="x_n" eeimg="1"/> 的取值）。</p>

### 2.隐马尔可夫模型
那么，**有没有一种方法即能将当前变量与更早的变量关联起来，又不需要那么多参数呢？当然，这里有一种非常强大的手段，即引入隐变量**，这是机器学习中用简单的基础部件表达丰富的模型的有力手段。

<p>这里如果假设隐变量构成一阶马尔科夫模型，而每个观测变量与一个隐变量关联，则可以得到一类模型的基础结构，即状态空间模型。如下图， <img src="https://www.zhihu.com/equation?tex=%5Cbm%7Bz_n%7D" alt="\bm{z_n}" eeimg="1"/> 为隐藏变量， <img src="https://www.zhihu.com/equation?tex=%5Cbm%7Bx_n%7D" alt="\bm{x_n}" eeimg="1"/> 为观测变量.</p><figure data-size="normal"><noscript><img src="https://pic4.zhimg.com/v2-cc1e028c988a799ccc54112ada47945d_b.jpg" data-caption="" data-size="normal" data-rawwidth="874" data-rawheight="273" class="origin_image zh-lightbox-thumb" width="874" data-original="https://pic4.zhimg.com/v2-cc1e028c988a799ccc54112ada47945d_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;874&#39; height=&#39;273&#39;&gt;&lt;/svg&gt;" data-caption="" data-size="normal" data-rawwidth="874" data-rawheight="273" class="origin_image zh-lightbox-thumb lazy" width="874" data-original="https://pic4.zhimg.com/v2-cc1e028c988a799ccc54112ada47945d_r.jpg" data-actualsrc="https://pic4.zhimg.com/v2-cc1e028c988a799ccc54112ada47945d_b.jpg"/></figure><p class="ztext-empty-paragraph"><br/></p><p>该类模型的关键是隐藏变量之间满足如下条件独立性，即在给定 <img src="https://www.zhihu.com/equation?tex=z_n" alt="z_n" eeimg="1"/> 时， <img src="https://www.zhihu.com/equation?tex=z_%7Bn-1%7D" alt="z_{n-1}" eeimg="1"/> 和 <img src="https://www.zhihu.com/equation?tex=z_%7Bn%2B1%7D" alt="z_{n+1}" eeimg="1"/> 条件独立：</p><figure data-size="normal"><noscript><img src="https://picb.zhimg.com/v2-fc54b531b7c50f41b0fb79c4acde4966_b.jpg" data-caption="" data-size="normal" data-rawwidth="267" data-rawheight="45" class="content_image" width="267"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;267&#39; height=&#39;45&#39;&gt;&lt;/svg&gt;" data-caption="" data-size="normal" data-rawwidth="267" data-rawheight="45" class="content_image lazy" width="267" data-actualsrc="https://picb.zhimg.com/v2-fc54b531b7c50f41b0fb79c4acde4966_b.jpg"/></figure><p>这类模型的联合分布可以表示为：</p><p><img src="https://www.zhihu.com/equation?tex=p%28%5Cbm%7Bx_1%7D%2C+%5Ccdots%2C+%5Cbm%7Bx_N%7D%2C+%5Cbm%7Bz_1%7D%2C+%5Ccdots%2C+%5Cbm%7Bz_N%7D%29+%3D+p%28%5Cbm%7Bz_1%7D%29+%5Cleft%5B+%5Cprod_%7Bn+%3D+2%7D%5E%7BN%7D%7Bp%28%5Cbm%7Bz_n%7D+%7C+%5Cbm%7Bz_%7Bn-1%7D%7D%29%7D+%5Cright%5D+%5Cleft%5B+%5Cprod_%7Bn+%3D+1%7D%5E%7BN%7D%7Bp%28%5Cbm%7Bx_n%7D+%7C+%5Cbm%7Bz_n%7D%29%7D+%5Cright%5D" alt="p(\bm{x_1}, \cdots, \bm{x_N}, \bm{z_1}, \cdots, \bm{z_N}) = p(\bm{z_1}) \left[ \prod_{n = 2}^{N}{p(\bm{z_n} | \bm{z_{n-1}})} \right] \left[ \prod_{n = 1}^{N}{p(\bm{x_n} | \bm{z_n})} \right]" eeimg="1"/>                 (5)</p><p>可见，看似很复杂的模型被分解成了简单的 <img src="https://www.zhihu.com/equation?tex=p%28%5Cbm%7Bz_1%7D%29" alt="p(\bm{z_1})" eeimg="1"/> 、 <img src="https://www.zhihu.com/equation?tex=p%28%5Cbm%7Bz_n%7D+%7C+%5Cbm%7Bz_%7Bn-1%7D%7D%29" alt="p(\bm{z_n} | \bm{z_{n-1}})" eeimg="1"/> 和 <img src="https://www.zhihu.com/equation?tex=p%28%5Cbm%7Bx_n%7D+%7C+%5Cbm%7Bz_n%7D%29" alt="p(\bm{x_n} | \bm{z_n})" eeimg="1"/> 三部分，这三者分别叫做<b>初始概率模型、转移概率模型和发射概率模型</b>，对状态空间模型建模实际就是对这三者进行建模。而且此时观测变量之间不再具有任何马尔科夫性，因为此时 <img src="https://www.zhihu.com/equation?tex=%5Cbm%7Bx_n%7D" alt="\bm{x_n}" eeimg="1"/> 的分布与其之前所有的观测变量都相关，无法从 <img src="https://www.zhihu.com/equation?tex=p%28%5Cbm%7Bx_n%7D+%7C+%5Cbm%7Bx_%7Bn-1%7D%7D%2C%5Ccdots%2C%5Cbm%7Bx_1%7D%29" alt="p(\bm{x_n} | \bm{x_{n-1}},\cdots,\bm{x_1})" eeimg="1"/> 的条件变量中拿掉任何一个变量，这样就将一个变量与其之前所有的变量关联起来了。这就是隐变量的能力。</p>

<p>当 <img src="https://www.zhihu.com/equation?tex=z_n" alt="z_n" eeimg="1"/> 为离散变量时，该状态空间模型即为隐马尔科夫模型。现在可以解释一下“隐马尔科夫”这个名字的含义了。“马尔科夫”自然是表示随机变量之间构成一个马尔科夫链了。而“隐”是指我们要推测的变量是未知的、隐藏的。正是这些隐藏的变量构成了马尔科夫链，所以就叫“隐马尔科夫”了。</p>

&nbsp;
##  三、模型表示
以下分别介绍HMM中初始概率模型、转移概率模型和发射概率模型的表示。
## 四、学习
HMM的参数学习采用最大似然法。在不知道其它更多信息的情况下，最大似然法是最通用也最合理的方法了，这也是大部分概率模型采用的学习方法。最大似然法的基本思想其实很简单、很直接，“已发生的即是概率最大的”，也就是说，它的目的是找到一组参数，使得在这组参数下，已观测到的事件或数据的联合概率最大。这一点从最大似然法的命名也好理解，“似”即“像”，表示概率；“然”即“这样”，表示已观测到的数据。“最大似然”即找到一组参数，使得“像这样”的概率最大。

然而与其它一些模型不同，用最大似然法求解HMM参数学习问题时，由于有隐变量的存在，无法直接求得参数的解析解，必须采用EM（Expectation Maximization）算法，逐步迭代直至收敛从而求得模型参数。值得庆幸的是，在实际应用中，EM算法收敛速度很快，基本经过一二十轮迭代、甚至几轮迭代即可达到收敛。

<p>EM算法分E步和M步两部分：在E步，假设已知模型参数，从而求得<b>隐变量的后验分布</b><img src="https://www.zhihu.com/equation?tex=p%28%5Cbm%7BZ%7D+%7C+%5Cbm%7BX%7D%2C+%5Cbm%7B%5Ctheta%7D+%5E+%7Bold%7D%29" alt="p(\bm{Z} | \bm{X}, \bm{\theta} ^ {old})" eeimg="1"/>；在M步，计算<b>全数据的对数似然</b>的在此后验分布下的期望的最大值，此即算法名称Expectation Maximization的来由。

## 五、推理
给定一组观测序列，根据以上描述的学习方法即可求出HMM的参数。然而大部分时候，求得模型的参数还不够，我们的最终目的往往是根据学习到的模型回答一些关心的问题，即推理。   
在HMM中，有两个问题是我们比较关心的，
```
第一个是预测问题，即给定一组观测变量，要预测下一个观测变量，该问题利用HMM的条件独立式和前述前向计算的结果可直接得到；
第二个是最大可能隐藏序列问题，即给定一组观测序列，如何找到与之对应的可能性最大的隐藏序列，该问题用维特比（Viterbi）算法可以高效求出。
```

## 六、缩放因子
**...它们的值都非常非常小，小到计算机已经无法精确表示。对于此类问题，有一种常用的数学技巧是对非常小的值取对数，从而将其转化为计算机可表示的对数值**。

<p>前面五部分已经将HMM的所有理论框架介绍完了。当我们高高兴兴地利用以上结论采用计算机实现解决实际问题时，会遇到另一个棘手的问题。观察<img src="https://www.zhihu.com/equation?tex=%5Calpha%28%5Cbm%7Bz_n%7D%29" alt="\alpha(\bm{z_n})" eeimg="1"/>、<img src="https://www.zhihu.com/equation?tex=%5Cbeta%28%5Cbm%7Bz_n%7D%29" alt="\beta(\bm{z_n})" eeimg="1"/>和<img src="https://www.zhihu.com/equation?tex=%5Comega%28%5Cbm%7Bz_n%7D%29" alt="\omega(\bm{z_n})" eeimg="1"/>的定义发现，它们要么是<img src="https://www.zhihu.com/equation?tex=n" alt="n" eeimg="1"/>个随机变量的条件联合分布，要么涉及到<img src="https://www.zhihu.com/equation?tex=n" alt="n" eeimg="1"/> 个概率值的相乘，因此它们的数量级为<img src="https://www.zhihu.com/equation?tex=1e%5E%7B-n%7D" alt="1e^{-n}" eeimg="1"/>，当<img src="https://www.zhihu.com/equation?tex=n" alt="n" eeimg="1"/>非常大时，它们的值都非常非常小，小到计算机已经无法精确表示。对于此类问题，有一种常用的数学技巧是对非常小的值取对数，从而将其转化为计算机可表示的对数值。该技巧对解决<img src="https://www.zhihu.com/equation?tex=%5Comega%28%5Cbm%7Bz_n%7D%29" alt="\omega(\bm{z_n})" eeimg="1"/>的数值“下溢”问题很有效。而对<img src="https://www.zhihu.com/equation?tex=%5Calpha%28%5Cbm%7Bz_n%7D%29" alt="\alpha(\bm{z_n})" eeimg="1"/>、<img src="https://www.zhihu.com/equation?tex=%5Cbeta%28%5Cbm%7Bz_n%7D%29" alt="\beta(\bm{z_n})" eeimg="1"/>的数值下溢问题，则需要一些公式推导，最后利用缩放因子解决此问题。

&nbsp;
## HMM的三个问题
### 1.概率计算问题
前向-后向算法 给定模型λ=(A,B,π)和观测序列Q={q1,q2,...,qT}，计算模型λ下观测到序列Q出现的概率P(Q|λ)；

#### 1.1、直接计算法(暴力算法)
类似KNN计算最近邻时候的算法。也就是说，暴力算法需要一个个遍历所有的状态去计算当前状态发生的概率。  
暴力计算法的计算量非常庞大。
#### 1.2、前向算法
前向和后向算法是运用某种递归(递推)的方式，帮助我们尽快得求解最终结果。

定义：给定λ，定义到时刻t部分观测序列为q1,q2,...,qt且状态为si的概率为前向概率。
#### 1.3、后向算法
定义：给定λ，定义到时刻t状态为si的前提下，从t+1到T部分观测序列为qt+1,qt+2,...,qT的概率为后向概率。
### 2.学习问题
Baum-Welch算法(状态未知) 已知观测序列Q={q1,q2,...,qT}，估计模型λ=(A,B,π)的参数，使得在该模型下观测序列P(Q|λ)最大。

Baum-Welch算法是EM算法的一个特例，专门用来求解隐马尔科夫中隐状态参数λ=(A,B,π)。  
即：根据已知的观测到序列 Q=白→黑→白→白→黑，去寻找整个模型的一组隐状态参数λ=(A,B,π)，使得在模型中观测序列发生的可能性P(Q|λ)最大。
### 3.预测问题
Viterbi算法 给定模型λ=(A,B,π)和观测序列Q={q1,q2,...,qT}，求给定观测序列条件概率P(I|Q，λ)最大的状态序列I。

已知观测到序列 Q=白→黑→白→白→黑，当我们得到λ=(A,B,π)后，我们用Viterbi算法 求出在哪一种状态序列发生的可能性最大，即，求出状态序列 I=①→③→②→②→③；即，抽取什么样的盒子顺序，更可能得到白→黑→白→白→黑这种结果。

&nbsp;
## reference
[隐马尔科夫模型](https://zhuanlan.zhihu.com/p/27907806)   
[02 隐马尔可夫模型 - HMM的三个问题 - 概率计算问题](https://www.jianshu.com/p/c80ca0aa4213)
