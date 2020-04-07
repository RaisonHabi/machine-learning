## 背景
The poor performance of our model maybe because, the model is too simple to describe the target, or may be model is too complex to express the target.   
Why this happened? And how to deal with it?

&nbsp;
## 模型表现
| 训练集表现 | 测试集表现 | 问题 |
| ------ | ------ | ------ |
| <期望值 | <期望值 | Underfitting |
| >期望值 | 接近或略逊于训练集 | 合适 |
| >期望值 | 远差于训练集 | Overfitting |

<p>怎么解决Underfitting和Overfitting问题？<br /></p>
<p>
</p><table style="border-collapse:collapse;border-spacing:0px;color:rgb(68,68,68);font-family:'Hiragino Sans GB', 'Microsoft Yahei', '微软雅黑', sans-serif;font-size:12px;"><thead><tr style="border-style:solid;border-width:1px 0px;border-color:rgb(204,204,204);"><th style="text-align:left;">问题</th>
<th style="text-align:left;">数据</th>
<th style="text-align:left;">特征</th>
<th style="text-align:left;">模型</th>
</tr></thead><tbody><tr style="border-style:solid;border-width:1px 0px;border-color:rgb(204,204,204);"><td style="vertical-align:top;">Underfitting</td>
<td style="vertical-align:top;">清洗数据</td>
<td style="vertical-align:top;">1. 增加特征<br />
2. 删除噪音特征</td>
<td style="vertical-align:top;">1. 调低正则项的惩罚参数<br />
2. 换更“复杂”的模型（如把线性模型换为非线性模型）<br />
3. 多个模型级联或组合</td>
</tr><tr style="border-style:solid;border-width:1px 0px;border-color:rgb(204,204,204);"><td style="vertical-align:top;">Overfitting</td>
<td style="vertical-align:top;">增加数据</td>
<td style="vertical-align:top;">1. 进行特征选择<br />
2. 降维（如对特征进行聚类、主题模型进行处理等）<br /></td>
<td style="vertical-align:top;">1. 提高正则项的惩罚参数<br />
2. 减少训练迭代次数<br />
3. 换更“简单”的模型（如把非线性模型换为线性模型）</td>
</tr></tbody></table><br /><p><br /></p>


过拟合与欠拟合也可以用 Bias 与 Variance 的角度来解释，<b>欠拟合会导致高 Bias ，过拟合会导致高 Variance ，所以模型需要在 Bias 与 Variance 之间做出一个权衡。</b></p><p>使用简单的模型去拟合复杂数据时，会导致模型很难拟合数据的真实分布，这时模型便欠拟合了，或者说有很大的 Bias，<b>Bias 即为模型的期望输出与其真实输出之间的差异</b>；有时为了得到比较精确的模型而过度拟合训练数据，或者模型复杂度过高时，可能连训练数据的噪音也拟合了，导致模型在训练集上效果非常好，但泛化性能却很差，这时模型便过拟合了，或者说有很大的 Variance，这时模型在不同训练集上得到的模型波动比较大，<b>Variance 刻画了不同训练集得到的模型的输出与这些模型期望输出的差异</b>。

&nbsp;
## 偏差、方差
<p><h3>偏差：</h3><b>描述的是预测值（估计值）的期望与真实值之间的差距。偏差越大，越偏离真实数据，如下图第二行所示。</b></p><p><h3>方差：</h3><b>描述的是预测值的变化范围，离散程度，也就是离其期望值的距离。方差越大，数据的分布越分散，如下图右列所示</b>。<figure><noscript><img src="https://pic4.zhimg.com/50/162bbe3ae6c8f46da4f4e05edea2d9fc_hd.jpg" data-rawwidth="936" data-rawheight="886" class="origin_image zh-lightbox-thumb" width="936" data-original="https://pic4.zhimg.com/162bbe3ae6c8f46da4f4e05edea2d9fc_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;936&#39; height=&#39;886&#39;&gt;&lt;/svg&gt;" data-rawwidth="936" data-rawheight="886" class="origin_image zh-lightbox-thumb lazy" width="936" data-original="https://pic4.zhimg.com/162bbe3ae6c8f46da4f4e05edea2d9fc_r.jpg" data-actualsrc="https://pic4.zhimg.com/50/162bbe3ae6c8f46da4f4e05edea2d9fc_hd.jpg"/></figure>参考：<a href="https://link.zhihu.com/?target=http%3A//scott.fortmann-roe.com/docs/BiasVariance.html" class=" wrap external" target="_blank" rel="nofollow noreferrer">Understanding the Bias-Variance Tradeoff</a></p></span></div>

&nbsp;

<p>在统计学中，一个模型好坏，是根据偏差和方差来衡量的，所以我们先来普及一下偏差(bias)和方差(variance)：</p>
<ul>
<li>偏差：描述的是预测值（估计值）的期望E’与真实值Y之间的差距。偏差越大，越偏离真实数据。</li>
</ul>
<ul>
<li>方差：描述的是预测值P的变化范围，离散程度，是预测值的方差，也就是离其期望值E的距离。方差越大，数据的分布越分散。</li>
</ul>
<p>模型的真实误差是两者之和，如公式{3}：</p>

<p>通常情况下，如果是小训练集，高偏差/低方差的分类器（例如，朴素贝叶斯NB）要比低偏差/高方差大分类的优势大（例如，KNN），因为后者会发生过拟合（overfiting）。然而，随着你训练集的增长，模型对于原数据的预测能力就越好，偏差就会降低，此时低偏差/高方差的分类器就会渐渐的表现其优势（因为它们有较低的渐近误差），而高偏差分类器这时已经不足以提供准确的模型了。</p>
<p>当然，你也可以认为这是生成模型（如NB）与判别模型（如KNN）的一个区别。</p>

<p>在实际中，为了让Error尽量小，我们在选择模型的时候需要平衡Bias和Variance所占的比例，也就是平衡over-fitting和under-fitting。</p>
<p>偏差、方差、模型复杂度三者之间的关系使用下图表示会更容易理解：</p>
<p><img src="/assets/articleImg/bias_variance.png" alt></p>
<p>当模型复杂度上升的时候，偏差会逐渐变小，而方差会逐渐变大。</p>

&nbsp;
## 拟合
<p>模型处于过拟合还是欠拟合，可以通过画出误差趋势图来观察。若模型在训练集与测试集上误差均很大，则说明模型的 Bias 很大，此时需要想办法处理 under-fitting ；若是训练误差与测试误差之间有个很大的 Gap ，则说明模型的 Variance 很大，这时需要想办法处理 over-fitting。 </p><figure><noscript><img src="https://pic1.zhimg.com/v2-026adcbc739640fc240b6e863ade5ea0_b.jpg" data-rawwidth="616" data-rawheight="218" class="origin_image zh-lightbox-thumb" width="616" data-original="https://pic1.zhimg.com/v2-026adcbc739640fc240b6e863ade5ea0_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;616&#39; height=&#39;218&#39;&gt;&lt;/svg&gt;" data-rawwidth="616" data-rawheight="218" class="origin_image zh-lightbox-thumb lazy" width="616" data-original="https://pic1.zhimg.com/v2-026adcbc739640fc240b6e863ade5ea0_r.jpg" data-actualsrc="https://pic1.zhimg.com/v2-026adcbc739640fc240b6e863ade5ea0_b.jpg"/></figure><p>一般在模型效果差的第一个想法是增多数据，其实增多数据并不一定会有更好的结果，因为<b>欠拟合时增多数据往往导致效果更差，而过拟合时增多数据会导致 Gap 的减小，效果不会好太多</b>，多以当模型效果很差时，应该检查模型是否处于欠拟合或者过拟合的状态，而不要一味的增多数据量，关于过拟合与欠拟合，这里给出几个解决方法。</p>

&nbsp;
## 如何解决欠拟合和过拟合问题
欠拟合问题，根本的原因是特征维度过少，导致拟合的函数无法满足训练集，误差较大。  
比如是识别一只狗狗的模型,可能二哈被提取的特征比较少，导致训练出来的模型不能很好地匹配，表现得很差，甚至二哈都无法识别    
**欠拟合问题可以通过增加特征维度来解决**。

过拟合问题，根本的原因则是特征维度过多，导致拟合的函数完美的经过训练集，但是对新数据的预测结果则较差。  
解决过拟合问题，则有2个途径：  
> 减少特征维度; 可以人工选择保留的特征，或者模型选择算法  
正则化; 保留所有的特征，通过降低参数θ的值，来影响模型

&nbsp;
<h3><b>常用解决办法</b></h3><p><b>(1)解决欠拟合的方法：</b></p><div class="highlight"><pre><code class="language-text">1、增加新特征，可以考虑加入进特征组合、高次特征，来增大假设空间;
2、尝试非线性模型，比如核SVM 、决策树、DNN等模型;
3、如果有正则项可以较小正则项参数 $\lambda$;
4、Boosting ,Boosting 往往会有较小的 Bias，比如 Gradient Boosting 等.</code></pre></div><p><b>(2)解决过拟合的方法：</b></p><div class="highlight"><pre><code class="language-text">1、交叉检验，通过交叉检验得到较优的模型参数;
2、特征选择，减少特征数或使用较少的特征组合，对于按区间离散化的特征，增大划分的区间;
3、正则化，常用的有 L1、L2 正则。而且 L1正则还可以自动进行特征选择;
4、如果有正则项则可以考虑增大正则项参数 lambda;
5、增加训练数据可以有限的避免过拟合;
6、Bagging ,将多个弱学习器Bagging 一下效果会好很多，比如随机森林等.</code></pre></div><p><b>（3）DNN中常见的方法：</b></p><div class="highlight"><pre><code class="language-text">1、早停策略。本质上是交叉验证策略，选择合适的训练次数，避免训练的网络过度拟合训练数据。
2、集成学习策略。而DNN可以用Bagging的思路来正则化。首先我们要对原始的m个训练样本进行有放回随机采样，构建N组m个样本的数据集，然后分别用这N组数据集去训练我们的DNN。即采用我们的前向传播算法和反向传播算法得到N个DNN模型的W,b参数组合，最后对N个DNN模型的输出用加权平均法或者投票法决定最终输出。不过用集成学习Bagging的方法有一个问题，就是我们的DNN模型本来就比较复杂，参数很多。现在又变成了N个DNN模型，这样参数又增加了N倍，从而导致训练这样的网络要花更加多的时间和空间。因此一般N的个数不能太多，比如5-10个就可以了。
3、DropOut策略。所谓的Dropout指的是在用前向传播算法和反向传播算法训练DNN模型时，一批数据迭代时，随机的从全连接DNN网络中去掉一部分隐藏层的神经元。　在对训练集中的一批数据进行训练时，我们随机去掉一部分隐藏层的神经元，并用去掉隐藏层的神经元的网络来拟合我们的一批训练数据。使用基于dropout的正则化比基于bagging的正则化简单，这显而易见，当然天下没有免费的午餐，由于dropout会将原始数据分批迭代，因此原始数据集最好较大，否则模型可能会欠拟合。</code></pre></div>

&nbsp;
## reference
[欠拟合（Underfitting） & 过拟合（Overfitting） & 正则化（Regularization）](https://blog.csdn.net/tz_zs/article/details/78588478)  
[机器学习算法比较](http://www.csuldw.com/2016/02/26/2016-02-26-choosing-a-machine-learning-classifier/)
