# 一、参考 [机器学习模型可解释性进行到底 —— SHAP值理论（一）](https://blog.csdn.net/sinat_26917383/article/details/115400327)
<h2>1、 [微观]单样本特征影响图一&#xff1a;waterfall</h2> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/0779776967274154c05eeea27bdc4374.png" alt="在这里插入图片描述" /></p> 
<p>图一&#xff1a;</p> 
<ul><li>该图代表第0个样本&#xff0c;</li><li>Y轴代表不同特征值&#xff0c;</li><li>X代表SHAP值&#xff0c;</li><li>E[f(x)]代表所有样本f(x)的期望&#xff0c;base_values&#xff0c;<code>model.predict(X)的预测值的平均值</code></li><li>f(x)代表第0个样本&#xff0c;f(x)值的大小为第0个样本的预测值&#xff0c; <code>model.predict(X[0]) &#61; base_values &#43; sum(shap_values[0].values) </code></li></ul> 
<p>红色代表&#xff0c;该特征对第0个样本是正向增益的。<br /> 蓝色为负向&#xff0c;由此可以看到哪些特征增益好 / 坏</p> 

```
import xgboost
import shap
shap.initjs()

# train an XGBoost model
X, y = shap.datasets.boston()
model = xgboost.XGBRegressor().fit(X, y)

# explain the model's predictions using SHAP
# (same syntax works for LightGBM, CatBoost, scikit-learn, transformers, Spark, etc.)
explainer = shap.Explainer(model)
shap_values = explainer(X)

# visualize the first prediction's explanation
shap.plots.waterfall(shap_values[0])
```
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/c87439d4b4256d257f109b73dba037f8.png" alt="在这里插入图片描述" /></p> 
<p>这个是第0个样本的 所有信息&#xff0c;其中</p> 
<ul><li>base_values -&gt; 平均&#xff0c;base_values&#xff0c;<code>model.predict(X)的预测值的平均值</code></li><li>values -&gt; 这一个样本&#xff0c;每个特征的SHAP值</li><li>data -&gt; 这一个样本&#xff0c;样本特征值</li></ul> 

## 
<h2>2、 [微观]单样本特征影响图二&#xff1a;force plot</h2> 
<p>整个理论的核心图&#xff1a;<br /> <img src="https://i-blog.csdnimg.cn/blog_migrate/7eb9e5ced856d5511cf17c8d66a49ac5.png" alt="在这里插入图片描述" /></p> 
<pre><code>shap.initjs()
# visualize the first prediction&#39;s explanation with a force plot
shap.plots.force(shap_values[0])
</code></pre> 
<p>shap值类似回归系数&#xff1a;有正负之分&#xff0c;大小之分</p> 
<p>如果解读这个图:</p> 
<ul><li>shap_values[0] - 第0个样本</li><li>shap_values[0].base_values - 22.53,所有样本汇总的平均 f(x) 值,所以所有值都一样&#xff0c;模型在数据集上的输出均值22.53</li><li>shap_values[0].data - 所有特征原值,本案例是数据集shape为<code>(506, 13)</code>,第0个样本13个特征的具体值</li><li>shap_values[0].values - 第0个样本13个特征的shap值</li></ul> 
<p>所以&#xff0c;图中所有样本基础平均f(x)值:22.53,该样本shap值加总之后为f(x) -&gt; 24.02</p> 
<p>计算方式:第0个样本特征原值 <code>第0个样本特征shap值 &#61; shap_values[0].data * shap_values[0].values</code></p> 
<ul><li>红色代表:正向影响&#xff0c;LSTAT这个特征&#xff0c;正向影响&#xff0c;shap值&#xff0c;4.98&#xff0c;影响最大&#xff1b;PTRATIO也是正向</li><li>蓝色代表&#xff1a;负向影响&#xff0c;RM特征&#xff0c;负向影响&#xff0c;shap值&#xff0c;6.575&#xff0c;负向的</li></ul> 
<p>来看一下&#xff1a;<code>shap_values[0]</code></p> 

## 
<h2>3、 [宏观]特征影响图</h2> 
<p>官方给到的code&#xff1a;</p> 
<pre><code># visualize all the training set predictions
shap.plots.force(shap_values)
</code></pre> 
<p>会一直报错&#xff1a;</p> 
<pre><code>Exception: In v0.20 force_plot now requires the base value as the first parameter! Try shap.force_plot(explainer.expected_value, shap_values) or for multi-output models try shap.force_plot(explainer.expected_value[0], shap_values[0]).
</code></pre> 
<p>所以&#xff0c;目前笔者测试的时候&#xff0c;需要按照这个公式&#xff1a;<code>shap.plots.force(平均f(x)值,shap值,特征重要性)</code><br /> 这里是可以自由选择样本数的&#xff0c;样本少&#xff0c;密度不大&#xff0c;看到的东西多一些:</p> 
<pre><code># 全样本
shap.plots.force(explainer.expected_value,shap_values.values,shap_values.data)

#前100个样本
shap.plots.force(explainer.expected_value,shap_values.values[:100],shap_values.data[:100])

</code></pre> 
<p>这张图的来源&#xff1a;</p> 
<p>上面的一个样本的解释图旋转90°&#xff0c;然后水平的堆积起所有的样本&#xff0c;就会出现上面的图片。这是全样本的解释图&#xff0c;我们可以选择不同的横纵坐标。</p> 
<ul><li>X - 横轴是样本数量&#xff0c;</li><li>Y - 纵轴是shap值加总&#xff08;每个特征值 * 每个特征的shap值&#xff09;</li></ul> 
<p>这里横轴的排列是非常有讲究的&#xff0c;因为不是按顺序排列的&#xff0c;该图会把受相同特征影响大的放一起,</p> 
<p>比如观察最左边&#xff0c;蓝色扎堆是负向shap增益区,划过可以看到基本是0/4/5/12这几个特征对大多数样本都有负向增益;<br /> 当然同样&#xff0c;右边&#xff0c;红色扎堆&#xff0c;12/5/10对一些样本是正向增益的</p> 
<p>整体来说&#xff0c;该图是一个宏观的了解&#xff0c;诸多样本不同的特征对其的影响<br /> <img src="https://i-blog.csdnimg.cn/blog_migrate/7f673d02cb08013fc501df27ca22cd75.png" alt="在这里插入图片描述" /><br /> 来看单点&#xff1a;</p> 
<p>jupyter中点击了&#xff0c;会静态&#xff0c;看到某个样本&#xff0c;不同特征的f(x)值<br /> <img src="https://i-blog.csdnimg.cn/blog_migrate/9e48e4f9357ccbd4e1e484ee49ab4dfc.png" alt="在这里插入图片描述" /><br /> 另外也可以指定一些样本&#xff1a;</p> 
<pre><code># visualize all the training set predictions
 # shap.plots.force(基础值,shap值,特征重要性)
shap.plots.force(explainer.expected_value,shap_values.values[:100],shap_values.data[:100])
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/a065f2aa7cc624913865a5d57c8c04e8.png" alt="在这里插入图片描述" /><br /> 整体会变得稀疏一些&#xff0c;看起来友好</p> 

## 
<h2>4、 [宏观]特征依赖图——dependence scatter plot</h2> 
<p>代表两个变量交互效应&#xff0c;这里借鉴文章<a href="https://mp.weixin.qq.com/s?__biz&#61;Mzg5ODAzMTkyMg&#61;&#61;&amp;mid&#61;2247488048&amp;idx&#61;1&amp;sn&#61;94ee7670b302c91f1f36d9805f3ab6d6&amp;chksm&#61;c0699a6df71e137b515e94dfc739079fa90c992513714badcee0098f3cfdeaf9eb87753ea674&amp;token&#61;1660368657&amp;lang&#61;zh_CN&amp;scene&#61;21#wechat_redirect" rel="nofollow">酒店排名模型中的商业价值度量</a></p> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/a97574a71ce09f214e6b1df35a1ffdb0.png" alt="在这里插入图片描述" /></p> 
<pre><code># create a dependence scatter plot to show the effect of a single feature across the whole dataset

shap.plots.scatter(shap_values[:,&#34;AGE&#34;], color&#61;shap_values[:,&#34;DIS&#34;])
</code></pre> 
<p>解读一下这个三维散点图&#xff0c;<strong>主要解释的是&#xff1a;NOX -&gt; AGE特征的影响</strong></p> 
<ul><li>X轴为AGE特征的特征值范围&#xff0c;</li><li>Y轴为AGE特征的shap值&#xff0c;对于模型的输出会带来的变化量</li></ul> 
<blockquote> 
 <p><em>其中我们可以发现对于同一个x 值&#xff0c;也就是特征取值相同的样本&#xff0c;它们的shap value不同。其原因是&#xff0c;该特征和其他特征有着交互相应</em></p> 
</blockquote> 
<p>右边是对比的特征NOX&#xff0c;这里红色代表NOX-高分部分&#xff1b;蓝色代表-NOX低分部分</p> 
<p>从图中可知&#xff1a;<br /> 最右边&#xff0c;一堆红色点&#xff0c;NOX-高分部分 对于 高年龄来说&#xff0c;shap值一般小于0&#xff0c;所以是负面影响<br /> 左上角&#xff0c;稀稀拉拉的蓝色点&#xff0c;代表&#xff0c;NOX-低分部分 对于 低年龄来说&#xff0c;shap值一般大于0&#xff0c;所以是正面影响</p> 
<p><strong>有个问题&#xff0c;貌似不能指定&#xff0c;与AGE对比的变量&#xff1f;</strong></p> 
<p>参考&#xff1a;<br /> <code>shap.plots.scatter(shap_values[:,&#34;AGE&#34;], color&#61;shap_values[:,&#34;DIS&#34;])</code><br /> 可以指定对比:AGE 与DIS之间的关系</p> 
<p>另外两个特征的影响&#xff0c;如果某个特征为分类特征&#xff0c;则会呈现序列装&#xff0c;如下图<br /> <a href="https://slundberg.github.io/shap/notebooks/tree_explainer/Census%20income%20classification%20with%20LightGBM.html" rel="nofollow">notebooks/tree_explainer</a>&#xff1a;</p> 
<p>如果是分类变量&#xff0c;笔者还没遇见过&#xff0c;只是看到文章<a href="https://blog.csdn.net/qq_41103204/article/details/104896630">可解释机器学习-shap value的使用</a>提及之前旧函数的一些注意事项&#xff1a;</p> 
<p>能够正常显示分类变量的结果。也就是说&#xff0c;如果希望后面正常使用shap 的全部功能的话&#xff0c;最好就是在刚开始的时候&#xff0c;我们先把分类变量转成数字形式&#xff0c;也就是OrdinalEncoder 编码。<br /> <img src="https://i-blog.csdnimg.cn/blog_migrate/87d65b584c75faec0b8b699a19f63ec2.png" alt="在这里插入图片描述" /></p> 

## 
<h2>5、 [宏观]特征密度散点图&#xff1a;beeswarm</h2> 
<pre><code># summarize the effects of all the features
shap.plots.beeswarm(shap_values)
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/b290803294d1d9bbcb44ba05367a3cdc.png" alt="在这里插入图片描述" /><br /> 下图中每一行代表一个特征&#xff0c;横坐标为Shap值。特征的排序是按照shap 的平均绝对值&#xff0c;对模型来说的最重要特征。宽的地方表示有大量的样本聚集。</p> 
<p>一个点代表一个样本&#xff0c;颜色越红说明特征本身数值越大&#xff0c;颜色越蓝说明特征本身数值越小。</p> 
<p>可以看做一种特征重要性的排列图&#xff0c;</p> 
<p>LSTAT对模型非常重要&#xff0c;而且LSTAT高分红点值的人&#xff0c;shap值小于0&#xff0c;负向影响&#xff1b;<br /> 特征值越小&#xff0c;shap大于0&#xff0c;正向影响</p> 
<p>横向来看&#xff0c;LSTAT这个特征&#xff0c;样本分布较为分散&#xff0c;那么代表该特征影响越大</p> 
<p>另外&#xff0c;比如特征 B &#xff0c;大多数的点弥漫在SHAP &#61; 0,所以对大部分人都没啥影响&#xff0c;只对小部分人有影响。</p>

## 
<h2>6、 [宏观]特征重要性SHAP值</h2> 
<p>每个特征的shap值排序&#xff0c;与上述的一致</p> 
<pre><code>shap.plots.bar(shap_values)
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/ab93c568ef40d3c619fd8994fd35750c.png" alt="在这里插入图片描述" /></p> 

## 
<h2>7、 [宏观]样本聚类下特征分布热力图</h2> 
<p>参考&#xff1a;<a href="https://shap.readthedocs.io/en/latest/example_notebooks/api_examples/plots/heatmap.html?highlight&#61;heatmap#heatmap-plot" rel="nofollow">heatmap plot</a></p> 
<pre><code>import xgboost
import shap

#train XGBoost model
X,y &#61; shap.datasets.adult()
model &#61; xgboost.XGBClassifier(n_estimators&#61;100, max_depth&#61;2).fit(X, y)

#compute SHAP values
explainer &#61; shap.Explainer(model, X)
shap_values &#61; explainer(X[:1000])

shap.plots.heatmap(shap_values)

</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/34f325fc91a0587bcd2135331ff200c2.png" alt="在这里插入图片描述" /><br /> 这个图里面有很多门道&#xff0c;</p> 
<ul><li>X轴下面是样本序列&#xff0c;X轴上面的f(x)是每个样本shap值加总&#xff08;样本维度shap加总&#xff09;&#xff0c;代表与均值的偏离程度&#xff1b;</li><li>Y轴左边是特征名&#xff0c;右边应该是特征重要性&#xff08;特征维度shap加总&#xff09;&#xff1b;</li><li>中间花花绿绿的条纹&#xff0c;是每个样本&#xff0c;每个特征&#xff0c;SHAP值大小。</li></ul> 
<p>另外还有&#xff0c;样本这样的排序是有规律的&#xff0c;先是<code>shap.order.hclust</code>层次聚类过&#xff0c;所以样本色块分布如此均匀。</p> 
<p>来具体解读一下&#xff0c;层次聚类的结果&#xff1a;</p> 
<ul><li>比如第0-50个样本&#xff0c;capital gain色块非常红&#xff0c;代表这50个样本&#xff0c;受capital gain的正向影响较大&#xff1b;且样本shap值加总f(x) 也大于平均线&#xff0c;是优质样本。</li></ul> 

## 
<h2>8、 [宏观]特征的层次聚类</h2> 
<pre><code>import xgboost
import shap

#train XGBoost model
X,y &#61; shap.datasets.adult()
model &#61; xgboost.XGBClassifier(n_estimators&#61;100, max_depth&#61;2).fit(X, y)

#compute SHAP values
explainer &#61; shap.Explainer(model, X)
shap_values &#61; explainer(X[:1000])

#层次聚类 &#43; SHAP值
clust &#61; shap.utils.hclust(X, y, linkage&#61;&#34;single&#34;)
shap.plots.bar(shap_values, clustering&#61;clust, clustering_cutoff&#61;1)
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/a9cf54ad6f5519dbab73681c0160180d.png" alt="在这里插入图片描述" /></p> 
<p>这其实是2.6节&#43;层次聚类的结果&#xff1b;<br /> 可以说&#xff0c;<code>relationship</code>和<code>marital status</code>高度相关&#xff0c;存在特征的多重共线性问题&#xff08;从另外一篇来看&#xff0c;可能<code>relationship</code>会混淆<code>marital status</code>的因果影响&#xff0c;参考<a href="https://shap.readthedocs.io/en/latest/example_notebooks/overviews/Be%20careful%20when%20interpreting%20predictive%20models%20in%20search%20of%20causal%C2%A0insights.html#" rel="nofollow">[Be careful when interpreting predictive models in search of causal insights]</a>&#xff09;</p> 

## 
<h2>9、 [微观]多样本-不同特征SHAP决策图</h2> 
<p><a href="https://shap.readthedocs.io/en/latest/example_notebooks/api_examples/plots/decision_plot.html" rel="nofollow">decision plot</a></p> 
<pre><code>from pprint import pprint
import lightgbm as lgb
import matplotlib.pyplot as plt
import numpy as np
import pickle
import shap
from sklearn.model_selection import train_test_split, StratifiedKFold
import warnings

X, y &#61; shap.datasets.adult()
X_display, y_display &#61; shap.datasets.adult(display&#61;True)

#create a train/test split
random_state &#61; 7
X_train, X_test, y_train, y_test &#61; train_test_split(X, y, test_size&#61;0.2, random_state&#61;random_state)
d_train &#61; lgb.Dataset(X_train, label&#61;y_train)
d_test &#61; lgb.Dataset(X_test, label&#61;y_test)

params &#61; {
    &#34;max_bin&#34;: 512,
    &#34;learning_rate&#34;: 0.05,
    &#34;boosting_type&#34;: &#34;gbdt&#34;,
    &#34;objective&#34;: &#34;binary&#34;,
    &#34;metric&#34;: &#34;binary_logloss&#34;,
    &#34;num_leaves&#34;: 10,
    &#34;verbose&#34;: -1,
    &#34;min_data&#34;: 100,
    &#34;boost_from_average&#34;: True,
    &#34;random_state&#34;: random_state
}

model &#61; lgb.train(params, d_train, 10000, valid_sets&#61;[d_test], early_stopping_rounds&#61;50, verbose_eval&#61;1000)

#计算shap值
explainer &#61; shap.TreeExplainer(model)
expected_value &#61; explainer.expected_value
if isinstance(expected_value, list):
    expected_value &#61; expected_value[1]
print(f&#34;Explainer expected value: {expected_value}&#34;)

select &#61; range(20)
features &#61; X_test.iloc[select]
features_display &#61; X_display.loc[features.index]

with warnings.catch_warnings():
    warnings.simplefilter(&#34;ignore&#34;)
    shap_values &#61; explainer.shap_values(features)[1]
    shap_interaction_values &#61; explainer.shap_interaction_values(features)
if isinstance(shap_interaction_values, list):
    shap_interaction_values &#61; shap_interaction_values[1]



</code></pre> 
<p>以上为加载数据&#xff0c;直接来画一下SHAP决策图&#xff1a;</p> 
<pre><code># top20 样本决策图
shap.decision_plot(expected_value, shap_values, features_display)
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/a854cf231ae8741b76d1909b73c3e10e.png" alt="在这里插入图片描述" /><br /> 该函数的输入有&#xff1a;</p> 
<ul><li>expected_value:<code>int</code>&#xff0c;预测值的平均值&#xff0c;<code>model.predict(X)的预测值的平均值</code></li><li>shap_values,<code>array</code>,20*12&#xff0c;20个样本12个特征下&#xff0c;各自的shap值</li><li>features_display,<code>dataframe</code>,20*12&#xff0c;20个样本12个特征下&#xff0c;各自的原特征值</li></ul> 
<p>该图的解读方式&#xff1a;</p> 
<ul><li>Y轴特征的排序就是特征的重要性&#xff0c;特征维度SHAP值的加总</li><li>横向&#xff0c;最上面<code>[-15,10]</code> 是这20个样本最终的预测值,<code>model.predict(x[i])</code></li><li>每条线是一个样本&#xff0c;所以比较适合样本比较少的分析</li><li>每条线最上面触碰到&#xff08;比如最右边的那条红线&#xff09;就是第9个样本的模型预测值&#xff08;<code>6.8</code>&#xff09;</li><li>中间那条灰线就是所有样本预测的均值&#xff0c;<code>expected_value &#61; -2.43</code></li></ul> 
<p>那么这个图在说个啥呢&#xff0c;就是每个样本&#xff0c;每个特征<strong>累加的过程</strong>&#xff0c;以第9个样本为例&#xff0c;以下是每个样本的SHAP值&#xff1a;</p> 
<pre><code>[(&#39;Age&#39;, 1.0005292304135247), (&#39;Workclass&#39;, -0.00961971822852331),
 (&#39;Education-Num&#39;, 0.6520195769846917), (&#39;Marital Status&#39;, 0.38046518229621656),
 (&#39;Occupation&#39;, 0.3068224161474528), (&#39;Relationship&#39;, 1.1042083100072628),
 (&#39;Race&#39;, 0.02055876376016164), (&#39;Sex&#39;, -0.2407685655486416),
 (&#39;Capital Gain&#39;, 6.091624680787399), (&#39;Capital Loss&#39;, -0.019415257138761084),
 (&#39;Hours per week&#39;, -0.05843492194589284), (&#39;Country&#39;, 0.011780342708710345)]
</code></pre> 
<p>累加的过程就是&#xff1a;</p> 
<pre><code>-2.43
&#43; Country,0.0117   
&#43; Capital Loss,-0.0194
&#43; ...
&#43; Capital Gain,6.091
&#43;...
</code></pre> 
<p>所以可以看到样本9的这条线在<code>Capital Gain</code>突然增加很多<br /> <img src="https://i-blog.csdnimg.cn/blog_migrate/01db402a72c05021bcd70fbf6c7ea4d1.png" alt="在这里插入图片描述" /></p> 
<p>同样该图也有一些变形&#xff1a;</p> 
<p><strong>变形1&#xff1a;由数值 -&gt; 概率</strong></p> 
<pre><code>shap.decision_plot(expected_value, shap_values, features_display, link&#61;&#39;logit&#39;)
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/e3e5e5488dba3a596b71b3344a983852.png" alt="在这里插入图片描述" /><br /> 最上面的[-10,15]改成了概率值</p> 
<p><strong>变形2&#xff1a;高亮某个样本线highlight</strong></p> 
<pre><code>shap.decision_plot(expected_value, shap_values, features_display, highlight&#61;9)
</code></pre> 
<p>比如这里高亮第9个样本&#xff1a;<br /> <img src="https://i-blog.csdnimg.cn/blog_migrate/d181f6b485188638db1c9eb5ca998785.png" alt="在这里插入图片描述" /></p> 

<br><br><br>

# 
# 二、参考 [金融风控实战—模型可解释之shap](https://blog.csdn.net/Grateful_Dead424/article/details/123978572)
<h2><a id="shapplotswaterfall_38"></a>shap.plots.waterfall瀑布图-展示单个用户变量影响</h2> 
<p>shap.explainer:<br /> This is the primary explainer interface for the SHAP library. It takes any combination of a model and masker and returns a callable subclass object that implements the particular estimation algorithm that was chosen.<br /> 这是 SHAP 库的主要解释器接口。它采用模型和掩码的任意组合&#xff0c;并返回一个可调用的子类对象&#xff0c;该对象实现所选的特定估计算法。</p> 

<p>SHAP瀑布图<br /> 可视化第一个预测的解释&#xff1a;</p> 

<pre><code class="prism language-python"><span class="token comment">#max_display显示y轴展现变量数量&#xff0c;默认参数是10</span>
shap<span class="token punctuation">.</span>plots<span class="token punctuation">.</span>waterfall<span class="token punctuation">(</span>shap_values1<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span><span class="token punctuation">,</span>max_display<span class="token operator">&#61;</span><span class="token number">20</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/c7fb7c9f596772db042a56d9b6c737d9.png" alt="在这里插入图片描述" /></p> 

<p>由于瀑布图只显示一个样本值的数据&#xff0c;我们无法看到变量的动态变化带来的影响</p> 
<h2>我们用散点图scatter来观察变量的动态变化</h2> 
<p>例如installment分期付款金额&#xff0c;整体趋势是这个值越大&#xff0c;shap value更高&#xff0c;坏客户概率越高</p> 
<pre><code class="prism language-python">shap<span class="token punctuation">.</span>plots<span class="token punctuation">.</span>scatter<span class="token punctuation">(</span>shap_values1<span class="token punctuation">[</span><span class="token punctuation">:</span><span class="token punctuation">,</span><span class="token string">&#34;installment&#34;</span><span class="token punctuation">]</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/f6d9e27c498810894fddba48dcc0b9f4.png" alt="在这里插入图片描述" /></p> 

<h2>force plot是针对单个样本预测的解释&#xff0c;它可以将shap values可视化为force&#xff0c;</h2> 
<p>每个特征值都是一个增加或减少预测的force&#xff0c;预测从基线开始&#xff0c;基线是解释模型的常数&#xff0c;每个归因值是一个箭头&#xff0c;增加&#xff08;正值&#xff09;或减少&#xff08;负值&#xff09;预测。<br /> 红色的为正贡献&#xff0c;蓝色为负贡献&#xff0c;对于第一个样本&#xff0c;由上图可以解释为特征total_pymnt&#61;639.9的正贡献第一&#xff0c;total_pymnt_inv&#61;639.9的正贡献第二&#xff0c;但是int_rate&#61;0.1033的负贡献最大<img src="https://i-blog.csdnimg.cn/blog_migrate/304c39a727e611fb24fb1c923591fe58.png" alt="在这里插入图片描述" /></p> 
<pre><code class="prism language-python"><span class="token comment"># 可视化第一个prediction的解释   如果不想用JS,传入matplotlib&#61;True</span>
shap<span class="token punctuation">.</span>force_plot<span class="token punctuation">(</span>explainer<span class="token punctuation">.</span>expected_value<span class="token punctuation">,</span> shap_values<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">,</span><span class="token punctuation">:</span><span class="token punctuation">]</span><span class="token punctuation">,</span> X<span class="token punctuation">.</span>iloc<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">,</span><span class="token punctuation">:</span><span class="token punctuation">]</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/fe40f421100a2b6245d83b9227121ebe.png" alt="在这里插入图片描述" /></p> 
<pre><code class="prism language-python">explainer<span class="token punctuation">.</span>expected_value
<span class="token comment">#-4.852247</span>
</code></pre> 
<pre><code class="prism language-python">shap<span class="token punctuation">.</span>force_plot<span class="token punctuation">(</span>explainer<span class="token punctuation">.</span>expected_value<span class="token punctuation">,</span> shap_values<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token punctuation">:</span><span class="token punctuation">]</span><span class="token punctuation">,</span> X<span class="token punctuation">.</span>iloc<span class="token punctuation">[</span><span class="token number">1</span><span class="token punctuation">,</span><span class="token punctuation">:</span><span class="token punctuation">]</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/bdf205cf6b747ac77042ad474d10e02f.png" alt="在这里插入图片描述" /><br /> 个图是直接由shap values绘成的&#xff0c;可以比较第一个样本的shap values具体数值&#xff1a;所以就算不绘制force plot&#xff0c;直接获取样本的shap values&#xff0c;就可以知道每个特征值是如何贡献得到模型预测值的。</p> 
<pre><code class="prism language-python">sample_0_shap <span class="token operator">&#61;</span> pd<span class="token punctuation">.</span>DataFrame<span class="token punctuation">(</span>X<span class="token punctuation">.</span>iloc<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">,</span><span class="token punctuation">:</span><span class="token punctuation">]</span><span class="token punctuation">)</span>
sample_0_shap<span class="token punctuation">.</span>rename<span class="token punctuation">(</span>columns<span class="token operator">&#61;</span><span class="token punctuation">{<!-- --></span><span class="token number">0</span><span class="token punctuation">:</span> <span class="token string">&#39;feature_value&#39;</span><span class="token punctuation">}</span><span class="token punctuation">,</span> inplace<span class="token operator">&#61;</span><span class="token boolean">True</span><span class="token punctuation">)</span>
sample_0_shap<span class="token punctuation">[</span><span class="token string">&#39;shap_value&#39;</span><span class="token punctuation">]</span> <span class="token operator">&#61;</span> shap_values<span class="token punctuation">[</span><span class="token number">0</span><span class="token punctuation">]</span>
sample_0_shap<span class="token punctuation">.</span>sort_values<span class="token punctuation">(</span><span class="token string">&#39;shap_value&#39;</span><span class="token punctuation">,</span> ascending<span class="token operator">&#61;</span><span class="token boolean">False</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/aee09d143f1dee90d2870606623f5f95.png" alt="在这里插入图片描述" /></p> 
<h2><a id="shapplotsbar_189"></a>shap.plots.bar</h2> 
<pre><code class="prism language-python"><span class="token comment">#绘图feature importance</span>
shap<span class="token punctuation">.</span>summary_plot<span class="token punctuation">(</span>shap_values<span class="token punctuation">,</span> X<span class="token punctuation">,</span> plot_type<span class="token operator">&#61;</span><span class="token string">&#34;bar&#34;</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/b032a22659313fd63fae9f54de134072.png" alt="在这里插入图片描述" /></p> 
<p>汇总&#xff1a;installment分期金额&#xff0c;total_rec_prncp迄今收到金额&#xff0c;int_rate贷款利率为前三最重要变量</p> 
<p>其实如果要查看特征值大小与预测影响之间的关系的话&#xff0c;第二种图还是不够清楚&#xff0c;所以这里主要讲第一种图&#xff0c;第一种图其实就是对shap values按照特征维度聚合计算平均绝对值&#xff0c;也就是​&#xff0c;使用该公式去计算shap values&#xff0c;可得到下表&#xff1a;</p> 
<pre><code class="prism language-python">feature_importance <span class="token operator">&#61;</span> pd<span class="token punctuation">.</span>DataFrame<span class="token punctuation">(</span><span class="token punctuation">)</span>
feature_importance<span class="token punctuation">[</span><span class="token string">&#39;feature&#39;</span><span class="token punctuation">]</span> <span class="token operator">&#61;</span> X<span class="token punctuation">.</span>columns
feature_importance<span class="token punctuation">[</span><span class="token string">&#39;importance&#39;</span><span class="token punctuation">]</span> <span class="token operator">&#61;</span> np<span class="token punctuation">.</span><span class="token builtin">abs</span><span class="token punctuation">(</span>shap_values<span class="token punctuation">)</span><span class="token punctuation">.</span>mean<span class="token punctuation">(</span><span class="token number">0</span><span class="token punctuation">)</span>
feature_importance<span class="token punctuation">.</span>sort_values<span class="token punctuation">(</span><span class="token string">&#39;importance&#39;</span><span class="token punctuation">,</span> ascending<span class="token operator">&#61;</span><span class="token boolean">False</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/39f1a86c3656b43724e5cf7090a6d96e.png" alt="在这里插入图片描述" /></p> 
<h2><a id="summary_plot_208"></a>summary_plot</h2> 
<p>summary plot 为每个样本绘制其每个特征的SHAP值&#xff0c;这可以更好地理解整体模式&#xff0c;并允许发现预测异常值。每一行代表一个特征&#xff0c;横坐标为SHAP值。一个点代表一个样本&#xff0c;颜色表示特征值(红色高&#xff0c;蓝色低)。比如&#xff0c;这张图表明installment分期付款金额值越高&#xff0c;会降提升坏客户概率</p> 
<pre><code class="prism language-python">shap<span class="token punctuation">.</span>summary_plot<span class="token punctuation">(</span>shap_values<span class="token punctuation">,</span> X<span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/9b39c4747c1c809035286cf5dee8f338.png" alt="在这里插入图片描述" /><br /> Interaction Values</p> 
<p>interaction value是将SHAP值推广到更高阶交互的一种方法。树模型实现了快速、精确的两两交互计算&#xff0c;这将为每个预测返回一个矩阵&#xff0c;其中主要影响在对角线上&#xff0c;交互影响在对角线外。这些数值往往揭示了有趣的隐藏关系(交互作用)</p> 
<pre><code class="prism language-python"><span class="token comment">#当数据量大时&#xff0c;shap_interaction_values函数调用时非常耗时&#xff0c;建议关闭电脑其他应用&#xff0c;加速计算。</span>
shap_interaction_values <span class="token operator">&#61;</span> explainer<span class="token punctuation">.</span>shap_interaction_values<span class="token punctuation">(</span>X<span class="token punctuation">)</span>
shap<span class="token punctuation">.</span>summary_plot<span class="token punctuation">(</span>shap_interaction_values<span class="token punctuation">,</span> X<span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/a6bcae3cd74996cee60e8d0023300cdd.png" alt="在这里插入图片描述" /></p> 
<h2><a id="dependence_plot_227"></a>dependence_plot</h2> 
<p>如果要看特征值大小与预测影响之间的关系使用dependence plot更合适&#xff0c;dependence plot清楚地展示了单个特征是如何影响模型的预测结果的&#xff0c;dependence plot同样有多种使用方式&#xff0c;一种是查看某个特征是如何影响到模型预测结果的&#xff0c;另一种是一个特征是如何和另一个特征交互影响到模型预测结果的。</p> 
<pre><code class="prism language-python">shap<span class="token punctuation">.</span>dependence_plot<span class="token punctuation">(</span><span class="token string">&#39;installment&#39;</span><span class="token punctuation">,</span> shap_values<span class="token punctuation">,</span> X<span class="token punctuation">,</span> interaction_index<span class="token operator">&#61;</span><span class="token boolean">None</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/2b0a2112573924aa34d4aacee02f6f9b.png" alt="在这里插入图片描述" /><br /> 总结&#xff1a;installment分期金额越大&#xff0c;坏客户概率越高<br /> 此图和scatter散点图绘制效果一致</p> 
<pre><code class="prism language-python">shap<span class="token punctuation">.</span>dependence_plot<span class="token punctuation">(</span><span class="token string">&#39;installment&#39;</span><span class="token punctuation">,</span> shap_values<span class="token punctuation">,</span> X<span class="token punctuation">,</span> interaction_index<span class="token operator">&#61;</span><span class="token string">&#39;installment&#39;</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/8ea16b086be2b62c62d4080be13e67e1.png" alt="在这里插入图片描述" /></p> 
<pre><code class="prism language-python">shap<span class="token punctuation">.</span>dependence_plot<span class="token punctuation">(</span><span class="token string">&#39;int_rate&#39;</span><span class="token punctuation">,</span> shap_values<span class="token punctuation">,</span> X<span class="token punctuation">,</span> interaction_index<span class="token operator">&#61;</span><span class="token boolean">None</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/a9dad13b6b0b90b24a296b2f3c70c85d.png" alt="在这里插入图片描述" /></p> 
<pre><code class="prism language-python">shap<span class="token punctuation">.</span>dependence_plot<span class="token punctuation">(</span><span class="token string">&#39;installment&#39;</span><span class="token punctuation">,</span> shap_values<span class="token punctuation">,</span> X<span class="token punctuation">,</span> interaction_index<span class="token operator">&#61;</span><span class="token string">&#39;int_rate&#39;</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/4984d3069d7b75d18b01ac8682cd36d5.png" alt="在这里插入图片描述" /><br /> 汇总&#xff1a;installment分期金额越大&#xff0c;int_rate贷款利率越高&#xff0c;坏客户风险越大&#xff1b;但不绝对</p> 
<pre><code class="prism language-python">shap<span class="token punctuation">.</span>dependence_plot<span class="token punctuation">(</span><span class="token string">&#39;installment&#39;</span><span class="token punctuation">,</span> shap_values<span class="token punctuation">,</span> X<span class="token punctuation">,</span> interaction_index<span class="token operator">&#61;</span><span class="token string">&#39;total_rec_prncp&#39;</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/577e0800fe3a7fd4f7ba1aa4a017f1cc.png" alt="在这里插入图片描述" /><br /> 迄今收到的本金total_rec_prncp和installment分期金额交互明显</p> 
<pre><code class="prism language-python">shap<span class="token punctuation">.</span>dependence_plot<span class="token punctuation">(</span><span class="token string">&#39;installment&#39;</span><span class="token punctuation">,</span> shap_values<span class="token punctuation">,</span> X<span class="token punctuation">,</span> interaction_index<span class="token operator">&#61;</span><span class="token string">&#39;emp_length&#39;</span><span class="token punctuation">)</span>
</code></pre> 
<p><img src="https://i-blog.csdnimg.cn/blog_migrate/a6bc58dde882445ded3a468423ea144b.png" alt="在这里插入图片描述" /><br /> 汇总&#xff1a;installment分期金额越大&#xff0c;emp_length对installment分期金额影响不明显&#xff0c;红色蓝色点几乎均匀混杂在一起</p>
