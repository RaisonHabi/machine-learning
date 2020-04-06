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

&nbsp;
## 偏差、方差
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
## 如何解决欠拟合和过拟合问题
欠拟合问题，根本的原因是特征维度过少，导致拟合的函数无法满足训练集，误差较大。  
比如是识别一只狗狗的模型,可能二哈被提取的特征比较少，导致训练出来的模型不能很好地匹配，表现得很差，甚至二哈都无法识别    
**欠拟合问题可以通过增加特征维度来解决**。

过拟合问题，根本的原因则是特征维度过多，导致拟合的函数完美的经过训练集，但是对新数据的预测结果则较差。  
解决过拟合问题，则有2个途径：  
> 减少特征维度; 可以人工选择保留的特征，或者模型选择算法  
正则化; 保留所有的特征，通过降低参数θ的值，来影响模型

&nbsp;

## reference
[欠拟合（Underfitting） & 过拟合（Overfitting） & 正则化（Regularization）](https://blog.csdn.net/tz_zs/article/details/78588478)  
[机器学习算法比较](http://www.csuldw.com/2016/02/26/2016-02-26-choosing-a-machine-learning-classifier/)
