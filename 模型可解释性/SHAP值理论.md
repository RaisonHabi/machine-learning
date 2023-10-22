## 
<p><img src="https://img-blog.csdnimg.cn/img_convert/79237b106d9e0ce6cc8ac63eaa85548b.png" alt="" /><br /> 最近在系统性的学习AUTOML一些细节&#xff0c;本篇单纯从实现与解读的角度入手&#xff0c;<br /> 因为最近SHAP版本与之前的调用方式有蛮多差异&#xff0c;就从新版本出发&#xff0c;进行解读。</p> 
<p>不会过多解读SHAP值理论部分&#xff0c;相关理论可参考&#xff1a;</p> 
<ul><li><a href="https://zhuanlan.zhihu.com/p/79556002">能解释树模型的Shap值究竟是个啥&#xff1f;</a></li><li><a href="https://zhuanlan.zhihu.com/p/85791430">SHAP知识点全汇总</a></li></ul> 
<p>关于SHAP值加速可参考以下几位大佬的文章&#xff1a;</p> 
<ul><li><a href="https://zhuanlan.zhihu.com/p/299337859">高效的ShapValue计算 - TreeShap分析</a></li></ul> 
<p>官方&#xff1a;<br /> <a href="https://github.com/slundberg/shap">slundberg/shap</a></p> 
<p>关联文章&#xff1a;<br /> <a href="https://blog.csdn.net/sinat_26917383/article/details/115556182">机器学习模型可解释性进行到底 —— 从SHAP值到预测概率&#xff08;二&#xff09;</a><br /> <a href="https://mattzheng.blog.csdn.net/article/details/115669705">机器学习模型可解释性进行到底 ——PDP&amp;ICE图&#xff08;三&#xff09;</a></p> 
<hr /> 
<p></p> 
<div class="toc"> 
 <h4>文章目录</h4> 
 <ul><li><a href="#1__26">1 介绍</a></li><li><a href="#2__63">2 可解释图</a></li><li><ul><li><a href="#21_waterfall_73">2.1 [微观]单样本特征影响图一&#xff1a;waterfall</a></li><li><a href="#22_force_plot_116">2.2 [微观]单样本特征影响图二&#xff1a;force plot</a></li><li><a href="#23__162">2.3 [宏观]特征影响图</a></li><li><a href="#24_dependence_scatter_plot_212">2.4 [宏观]特征依赖图——dependence scatter plot</a></li><li><a href="#25_beeswarm_254">2.5 [宏观]特征密度散点图&#xff1a;beeswarm</a></li><li><a href="#26__SHAP_275">2.6 [宏观]特征重要性SHAP值</a></li><li><a href="#27___283">2.7 [宏观]样本聚类下特征分布热力图</a></li><li><a href="#28__312">2.8 [宏观]特征的层次聚类</a></li><li><a href="#29_SHAP_336">2.9 [微观]多样本-不同特征SHAP决策图</a></li></ul> 
  </li><li><a href="#3__457">3 优质解读案例</a></li><li><ul><li><a href="#31__459">3.1 酒店排名模型中的商业价值度量</a></li></ul> 
  </li><li><a href="#4__478">4 一致的个性化特征归因方法</a></li><li><a href="#5_base_values__shap_522">5 详解base_values 和 单样本shap值的计算过程</a></li><li><a href="#6__571">6 其他细节的延申</a></li><li><ul><li><a href="#61_waterfall____572">6.1 waterfall图&#xff0c;从只能画一人 -&gt; 支持多人</a></li><li><a href="#62_shap___612">6.2 一个完整的shap值重要性输出 案例</a></li><li><a href="#63__658">6.3 画图支持中文</a></li></ul> 
 </li></ul> 
</div> 
<p></p> 
<hr /> 
<h2><a id="1__26"></a>1 介绍</h2> 
<p>文章<a href="https://blog.csdn.net/weixin_44803791/article/details/109776357">可解释性机器学习_Feature Importance、Permutation Importance、SHAP</a><br /> 来看一下SHAP模型&#xff0c;是比较全能的模型可解释性的方法&#xff0c;既可作用于之前的全局解释&#xff0c;也可以局部解释&#xff0c;即单个样本来看&#xff0c;模型给出的预测值和某些特征可能的关系&#xff0c;这就可以用到SHAP。</p> 
<p>SHAP 属于模型事后解释的方法&#xff0c;它的核心思想是计算特征对模型输出的边际贡献&#xff0c;再从全局和局部两个层面对“黑盒模型”进行解释。SHAP构建一个加性的解释模型&#xff0c;所有的特征都视为“贡献者”。</p> 
<p>对于每个预测样本&#xff0c;模型都产生一个预测值&#xff0c;SHAP value就是该样本中每个特征所分配到的数值。</p> 
<p>基本思想&#xff1a;计算一个特征加入到模型时的边际贡献&#xff0c;然后考虑到该特征在所有的特征序列的情况下不同的边际贡献&#xff0c;取均值&#xff0c;即某该特征的SHAPbaseline value</p> 
<p>文章<a href="https://yyqing.me/post/2018/2018-09-25-kaggle-model-insights">kaggle | Machine Learning for Insights Challenge</a>提及&#xff1a;<br /> Permutation importance很不错&#xff0c;因为它用很简单的数字就可以衡量特征对模型的重要性。但是它不能handle这么一种情况&#xff1a;当一个feature有中等的permutation importance的时候&#xff0c;这可能意味着这么两种情况&#xff1a;</p> 
<ul><li>1&#xff1a;对少量的预测有很大的影响&#xff0c;但是整体来说影响较小&#xff1b;</li><li>2&#xff1a;对所有的预测都有中等程度的影响。</li></ul> 
<p>SHAP 就可以应用的上&#xff0c;来看一下SHAP呈现的几种图&#xff0c;本轮笔者 是直接拿<a href="https://github.com/slundberg/shap">slundberg/shap</a>中的代码&#xff0c;发现0.39.0版本&#xff0c;跟之前的版本差异非常大&#xff0c;很多函数名称都发生了变化。</p> 
<p>笔者实验下述代码的环境&#xff1a;anaconda &#43; py3.7 &#43; jupyter notebook &#43; shap&#61;&#61;0.39.0</p> 
<p>安装方式&#xff1a;</p> 
<pre><code>
pip install shap
or
conda install -c conda-forge shap
</code></pre> 
<hr /> 
<h2><a id="2__63"></a>2 可解释图</h2> 
<p>那就从拆解当下<a href="https://github.com/slundberg/shap">slundberg/shap</a>的案例入手&#xff0c;开始解读SHAP值的各类神图&#xff1a;</p> 
<p>注意画图前需要加&#xff1a;</p> 
<pre><code>shap.initjs()
</code></pre> 
<h3><a id="21_waterfall_73"></a>2.1 [微观]单样本特征影响图一&#xff1a;waterfall</h3> 
<p><img src="https://img-blog.csdnimg.cn/20210406111339400.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /></p> 
<p>图一&#xff1a;</p> 
<ul><li>该图代表第0个样本&#xff0c;</li><li>Y轴代表不同特征值&#xff0c;</li><li>X代表SHAP值&#xff0c;</li><li>E[f(x)]代表所有样本f(x)的期望&#xff0c;base_values&#xff0c;<code>model.predict(X)的预测值的平均值</code></li><li>f(x)代表第0个样本&#xff0c;f(x)值的大小为第0个样本的预测值&#xff0c; <code>model.predict(X[0]) &#61; base_values &#43; sum(shap_values[0].values) </code></li></ul> 
<p>红色代表&#xff0c;该特征对第0个样本是正向增益的。<br /> 蓝色为负向&#xff0c;由此可以看到哪些特征增益好 / 坏</p> 
<pre><code>
import xgboost
import shap
shap.initjs()

# train an XGBoost model
X, y &#61; shap.datasets.boston()
model &#61; xgboost.XGBRegressor().fit(X, y)

# explain the model&#39;s predictions using SHAP
# (same syntax works for LightGBM, CatBoost, scikit-learn, transformers, Spark, etc.)
explainer &#61; shap.Explainer(model)
shap_values &#61; explainer(X)

# visualize the first prediction&#39;s explanation
shap.plots.waterfall(shap_values[0])

</code></pre> 
<p><img src="https://img-blog.csdnimg.cn/20210406112834710.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /></p> 
<p>这个是第0个样本的 所有信息&#xff0c;其中</p> 
<ul><li>base_values -&gt; 平均&#xff0c;base_values&#xff0c;<code>model.predict(X)的预测值的平均值</code></li><li>values -&gt; 这一个样本&#xff0c;每个特征的SHAP值</li><li>data -&gt; 这一个样本&#xff0c;样本特征值</li></ul> 
<h3><a id="22_force_plot_116"></a>2.2 [微观]单样本特征影响图二&#xff1a;force plot</h3> 
<p>整个理论的核心图&#xff1a;<br /> <img src="https://img-blog.csdnimg.cn/2021040611295367.png" alt="在这里插入图片描述" /></p> 
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
<pre><code>
.values &#61;
array([-4.2850167e-01, -6.6636719e-02,  7.7860229e-02, -1.5295845e-03,
       -7.2922713e-01, -2.1700280e&#43;00,  1.9213372e-01, -4.1425934e-01,
       -4.9156108e-01, -4.7296646e-01,  2.5669456e-01, -5.3907130e-02,
        5.7883248e&#43;00], dtype&#61;float32)

.base_values &#61;
22.532942

.data &#61;
array([6.320e-03, 1.800e&#43;01, 2.310e&#43;00, 0.000e&#43;00, 5.380e-01, 6.575e&#43;00,
       6.520e&#43;01, 4.090e&#43;00, 1.000e&#43;00, 2.960e&#43;02, 1.530e&#43;01, 3.969e&#43;02,
       4.980e&#43;00])

</code></pre> 
<h3><a id="23__162"></a>2.3 [宏观]特征影响图</h3> 
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

# 前100个样本
shap.plots.force(explainer.expected_value,shap_values.values[:100],shap_values.data[:100])

</code></pre> 
<p>这张图的来源&#xff1a;</p> 
<p>上面的一个样本的解释图旋转90°&#xff0c;然后水平的堆积起所有的样本&#xff0c;就会出现上面的图片。这是全样本的解释图&#xff0c;我们可以选择不同的横纵坐标。</p> 
<ul><li>X - 横轴是样本数量&#xff0c;</li><li>Y - 纵轴是shap值加总&#xff08;每个特征值 * 每个特征的shap值&#xff09;</li></ul> 
<p>这里横轴的排列是非常有讲究的&#xff0c;因为不是按顺序排列的&#xff0c;该图会把受相同特征影响大的放一起,</p> 
<p>比如观察最左边&#xff0c;蓝色扎堆是负向shap增益区,划过可以看到基本是0/4/5/12这几个特征对大多数样本都有负向增益;<br /> 当然同样&#xff0c;右边&#xff0c;红色扎堆&#xff0c;12/5/10对一些样本是正向增益的</p> 
<p>整体来说&#xff0c;该图是一个宏观的了解&#xff0c;诸多样本不同的特征对其的影响<br /> <img src="https://img-blog.csdnimg.cn/20210406113735691.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /><br /> 来看单点&#xff1a;</p> 
<p>jupyter中点击了&#xff0c;会静态&#xff0c;看到某个样本&#xff0c;不同特征的f(x)值<br /> <img src="https://img-blog.csdnimg.cn/2021040611385448.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /><br /> 另外也可以指定一些样本&#xff1a;</p> 
<pre><code># visualize all the training set predictions
 # shap.plots.force(基础值,shap值,特征重要性)
shap.plots.force(explainer.expected_value,shap_values.values[:100],shap_values.data[:100])
</code></pre> 
<p><img src="https://img-blog.csdnimg.cn/20210406113919348.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /><br /> 整体会变得稀疏一些&#xff0c;看起来友好</p> 
<h3><a id="24_dependence_scatter_plot_212"></a>2.4 [宏观]特征依赖图——dependence scatter plot</h3> 
<p>代表两个变量交互效应&#xff0c;这里借鉴文章<a href="https://mp.weixin.qq.com/s?__biz&#61;Mzg5ODAzMTkyMg&#61;&#61;&amp;mid&#61;2247488048&amp;idx&#61;1&amp;sn&#61;94ee7670b302c91f1f36d9805f3ab6d6&amp;chksm&#61;c0699a6df71e137b515e94dfc739079fa90c992513714badcee0098f3cfdeaf9eb87753ea674&amp;token&#61;1660368657&amp;lang&#61;zh_CN&amp;scene&#61;21#wechat_redirect">酒店排名模型中的商业价值度量</a></p> 
<p><img src="https://img-blog.csdnimg.cn/2021040611460216.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /></p> 
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
<p>另外两个特征的影响&#xff0c;如果某个特征为分类特征&#xff0c;则会呈现序列装&#xff0c;如下图<br /> <a href="https://slundberg.github.io/shap/notebooks/tree_explainer/Census%20income%20classification%20with%20LightGBM.html">notebooks/tree_explainer</a>&#xff1a;</p> 
<p>如果是分类变量&#xff0c;笔者还没遇见过&#xff0c;只是看到文章<a href="https://blog.csdn.net/qq_41103204/article/details/104896630">可解释机器学习-shap value的使用</a>提及之前旧函数的一些注意事项&#xff1a;</p> 
<p>能够正常显示分类变量的结果。也就是说&#xff0c;如果希望后面正常使用shap 的全部功能的话&#xff0c;最好就是在刚开始的时候&#xff0c;我们先把分类变量转成数字形式&#xff0c;也就是OrdinalEncoder 编码。<br /> <img src="https://img-blog.csdnimg.cn/20210406115511332.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /></p> 
<h3><a id="25_beeswarm_254"></a>2.5 [宏观]特征密度散点图&#xff1a;beeswarm</h3> 
<pre><code># summarize the effects of all the features
shap.plots.beeswarm(shap_values)
</code></pre> 
<p><img src="https://img-blog.csdnimg.cn/2021040611485262.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /><br /> 下图中每一行代表一个特征&#xff0c;横坐标为Shap值。特征的排序是按照shap 的平均绝对值&#xff0c;对模型来说的最重要特征。宽的地方表示有大量的样本聚集。</p> 
<p>一个点代表一个样本&#xff0c;颜色越红说明特征本身数值越大&#xff0c;颜色越蓝说明特征本身数值越小。</p> 
<p>可以看做一种特征重要性的排列图&#xff0c;</p> 
<p>LSTAT对模型非常重要&#xff0c;而且LSTAT高分红点值的人&#xff0c;shap值小于0&#xff0c;负向影响&#xff1b;<br /> 特征值越小&#xff0c;shap大于0&#xff0c;正向影响</p> 
<p>横向来看&#xff0c;LSTAT这个特征&#xff0c;样本分布较为分散&#xff0c;那么代表该特征影响越大</p> 
<p>另外&#xff0c;比如特征 B &#xff0c;大多数的点弥漫在SHAP &#61; 0,所以对大部分人都没啥影响&#xff0c;只对小部分人有影响。</p> 
<h3><a id="26__SHAP_275"></a>2.6 [宏观]特征重要性SHAP值</h3> 
<p>每个特征的shap值排序&#xff0c;与上述的一致</p> 
<pre><code>shap.plots.bar(shap_values)
</code></pre> 
<p><img src="https://img-blog.csdnimg.cn/20210406114937726.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /></p> 
<h3><a id="27___283"></a>2.7 [宏观]样本聚类下特征分布热力图</h3> 
<p>参考&#xff1a;<a href="https://shap.readthedocs.io/en/latest/example_notebooks/api_examples/plots/heatmap.html?highlight&#61;heatmap#heatmap-plot">heatmap plot</a></p> 
<pre><code>import xgboost
import shap

# train XGBoost model
X,y &#61; shap.datasets.adult()
model &#61; xgboost.XGBClassifier(n_estimators&#61;100, max_depth&#61;2).fit(X, y)

# compute SHAP values
explainer &#61; shap.Explainer(model, X)
shap_values &#61; explainer(X[:1000])

shap.plots.heatmap(shap_values)

</code></pre> 
<p><img src="https://img-blog.csdnimg.cn/e8dc8a1621ba4b6386302d05989dbe07.png" alt="在这里插入图片描述" /><br /> 这个图里面有很多门道&#xff0c;</p> 
<ul><li>X轴下面是样本序列&#xff0c;X轴上面的f(x)是每个样本shap值加总&#xff08;样本维度shap加总&#xff09;&#xff0c;代表与均值的偏离程度&#xff1b;</li><li>Y轴左边是特征名&#xff0c;右边应该是特征重要性&#xff08;特征维度shap加总&#xff09;&#xff1b;</li><li>中间花花绿绿的条纹&#xff0c;是每个样本&#xff0c;每个特征&#xff0c;SHAP值大小。</li></ul> 
<p>另外还有&#xff0c;样本这样的排序是有规律的&#xff0c;先是<code>shap.order.hclust</code>层次聚类过&#xff0c;所以样本色块分布如此均匀。</p> 
<p>来具体解读一下&#xff0c;层次聚类的结果&#xff1a;</p> 
<ul><li>比如第0-50个样本&#xff0c;capital gain色块非常红&#xff0c;代表这50个样本&#xff0c;受capital gain的正向影响较大&#xff1b;且样本shap值加总f(x) 也大于平均线&#xff0c;是优质样本。</li></ul> 
<h3><a id="28__312"></a>2.8 [宏观]特征的层次聚类</h3> 
<pre><code>import xgboost
import shap

# train XGBoost model
X,y &#61; shap.datasets.adult()
model &#61; xgboost.XGBClassifier(n_estimators&#61;100, max_depth&#61;2).fit(X, y)

# compute SHAP values
explainer &#61; shap.Explainer(model, X)
shap_values &#61; explainer(X[:1000])

# 层次聚类 &#43; SHAP值
clust &#61; shap.utils.hclust(X, y, linkage&#61;&#34;single&#34;)
shap.plots.bar(shap_values, clustering&#61;clust, clustering_cutoff&#61;1)
</code></pre> 
<p><img src="https://img-blog.csdnimg.cn/8848bb6d3c044923bfa367b3037c1316.png" alt="在这里插入图片描述" /></p> 
<p>这其实是2.6节&#43;层次聚类的结果&#xff1b;<br /> 可以说&#xff0c;<code>relationship</code>和<code>marital status</code>高度相关&#xff0c;存在特征的多重共线性问题&#xff08;从另外一篇来看&#xff0c;可能<code>relationship</code>会混淆<code>marital status</code>的因果影响&#xff0c;参考<a href="https://shap.readthedocs.io/en/latest/example_notebooks/overviews/Be%20careful%20when%20interpreting%20predictive%20models%20in%20search%20of%20causal%C2%A0insights.html#">[Be careful when interpreting predictive models in search of causal insights]</a>&#xff09;</p> 
<h3><a id="29_SHAP_336"></a>2.9 [微观]多样本-不同特征SHAP决策图</h3> 
<p><a href="https://shap.readthedocs.io/en/latest/example_notebooks/api_examples/plots/decision_plot.html">decision plot</a></p> 
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

# create a train/test split
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

# 计算shap值
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
<p><img src="https://img-blog.csdnimg.cn/e7618cfcb8a741fea5411b31194b19b0.png" alt="在这里插入图片描述" /><br /> 该函数的输入有&#xff1a;</p> 
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
<p>所以可以看到样本9的这条线在<code>Capital Gain</code>突然增加很多<br /> <img src="https://img-blog.csdnimg.cn/c4250ac610f64dc2acbcc7e06e036822.png" alt="在这里插入图片描述" /></p> 
<p>同样该图也有一些变形&#xff1a;</p> 
<p><strong>变形1&#xff1a;由数值 -&gt; 概率</strong></p> 
<pre><code>shap.decision_plot(expected_value, shap_values, features_display, link&#61;&#39;logit&#39;)
</code></pre> 
<p><img src="https://img-blog.csdnimg.cn/cd8588409adb4616b1e3612e719344de.png" alt="在这里插入图片描述" /><br /> 最上面的[-10,15]改成了概率值</p> 
<p><strong>变形2&#xff1a;高亮某个样本线highlight</strong></p> 
<pre><code>shap.decision_plot(expected_value, shap_values, features_display, highlight&#61;9)
</code></pre> 
<p>比如这里高亮第9个样本&#xff1a;<br /> <img src="https://img-blog.csdnimg.cn/2e7c700f73e54c68bc25a8cea906ba0b.png" alt="在这里插入图片描述" /></p> 
<hr /> 
<h2><a id="3__457"></a>3 优质解读案例</h2> 
<h3><a id="31__459"></a>3.1 酒店排名模型中的商业价值度量</h3> 
<p>截取文章&#xff1a;<a href="https://mp.weixin.qq.com/s?__biz&#61;Mzg5ODAzMTkyMg&#61;&#61;&amp;mid&#61;2247488048&amp;idx&#61;1&amp;sn&#61;94ee7670b302c91f1f36d9805f3ab6d6&amp;chksm&#61;c0699a6df71e137b515e94dfc739079fa90c992513714badcee0098f3cfdeaf9eb87753ea674&amp;token&#61;1660368657&amp;lang&#61;zh_CN&amp;scene&#61;21#wechat_redirect">酒店排名模型中的商业价值度量</a></p> 
<p>对模型的单个结果进行研究是有趣的&#xff0c;但是聚合视图可以让我们很好地查看模型给出的趋势。下面的摘要图按重要性降序列出了几个最重要的特征。每个点都是一个结果&#xff0c;它在x轴上的位置代表特征的SHAP值&#xff0c;颜色代表特征的相对大小&#xff0c;红色代表高&#xff0c;蓝色代表低。<br /> <img src="https://img-blog.csdnimg.cn/20210406172001450.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /><br /> 我们可以看到&#xff0c;最重要的特征是hotel_cumulative_share&#xff0c;在右侧有一个红色的大条&#xff0c;表示分享的多的酒店是好的。接下来是previous_user_hotel_interaction&#xff0c;这是一个标记&#xff0c;表示用户以前是否曾经浏览过该酒店。虽然这个标志通常设置为0表示没有交互&#xff0c;但是当它不是0时&#xff0c;它的影响是巨大的。<br /> 与股票或评论计数不同&#xff0c;较低的相对价格几乎总是被认为是更好的。一般来说&#xff0c;具有良好历史业绩的酒店&#xff0c;离用户申报的目的地较近的酒店(如果有的话)&#xff0c;以及相对便宜的酒店&#xff0c;都在模型中排名较高。</p> 
<p>我想扩展最后一点&#xff0c;因为之前我说过&#xff0c;一个好的机器学习模型应该能够为不同的用户找到一个合适的价格区间。虽然该模型认为相对便宜的酒店更好&#xff0c;但它强调&#xff0c;这是不正确的&#xff0c;有时根本不符合用户显示出对高端酒店的偏好。</p> 
<p><img src="https://img-blog.csdnimg.cn/20210406172010505.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /><br /> 上面是一个部分依赖图&#xff0c;显示了x轴user_preferred_price上用户的价格水平和酒店的价格srq_price_zscore之间的交互。<br /> 一条长长的红色线条大致沿着y-x线&#xff0c;这说明昂贵的酒店对于低端用户具有负 SHAP值&#xff0c;与低端用户的相关性较小&#xff0c;对于高端用户具有正 SHAP值&#xff0c;与高端用户的相关性更大。<br /> 相反&#xff0c;垂直的蓝色条纹表明&#xff0c;该模型能够调整其对低端用户的期望。在不需要任何人工输入的情况下&#xff0c;该模型能够根据用户指定的价格水平将其与酒店进行匹配。</p> 
<hr /> 
<h2><a id="4__478"></a>4 一致的个性化特征归因方法</h2> 
<p><a href="https://zhuanlan.zhihu.com/p/85791430">SHAP知识点全汇总</a></p> 
<p>对于可解释来说&#xff0c;一致性非常重要&#xff0c;稳定的归因方式才有稳定的解决。</p> 
<blockquote> 
 <p>一致性&#xff1a;每当我们更改模型以使其更依赖于某个特征时&#xff0c;该特征的归因重要性不应该降低。</p> 
</blockquote> 
<p>如果一致性不成立&#xff0c;意味着当一个模型被更改为某个特征对模型输出的影响更大时&#xff0c;反而会降低该特征的重要性&#xff0c;那么我们不能比较任意两个模型之间的归因重要性&#xff0c;因为具有较高分配归因的特征并不意味着模型实际上更依赖该特征。</p> 
<p>特征归因方法可以分全局和个性化&#xff08;针对个体&#xff09;&#xff0c;其中全局特征重要度是为整个数据集计算的&#xff0c;该类特征归因方法通常都用特征重要度表示&#xff0c;主要有三种方式:</p> 
<ul><li>增益&#xff08;Gain&#xff09;。给定特征的所有分裂所贡献的损失或不纯度的总减少量&#xff0c;增益在特征选择方向上被广泛应用。</li><li>分裂数&#xff08;Split Count&#xff09;。在所有树中一个特征被用做分裂节点的次数。</li><li>置换。随机置换测试集中一个特征的值&#xff0c;然后观察模型误差的变化&#xff0c;如果一个特征的值很重要&#xff0c;那么遍历它会导致模型的错误大量增加。</li></ul> 
<p>上面三种都是基于整个数据集去计算特征重要度的&#xff0c;但对于树来说&#xff0c;计算单个预测的特征重要值的个性化方法却较少&#xff0c;虽然与模型无关的个性化解释方法&#xff08;比如LIME&#xff09;可以应用于树&#xff0c;但它们明显比树特定的方法慢&#xff0c;并且具有抽样变异性&#xff0c;目前我们所知的树特有的个性化解释方法只有Sabbas&#xff0c;该方法与经典的全局的增益方法类似&#xff0c;但它不是测量损失的减少&#xff0c;而是测量模型预期输出的变化&#xff0c;通过比较模型在树根处输出的期望值与子树在子节点处输出的期望值&#xff0c;以及当前输入的决策路径&#xff0c;然后将这些期望之间的差异归因于在根节点上分离的特性&#xff0c;通过递归地重复这个过程&#xff0c;最后在决策路径上的特征之间分配预期模型输出和当前输出之间的差异。</p> 
<p>下面举两个模型的例子对归因方法的一致性进行比较&#xff0c;假设模型的输出是基于人的症状的风险评分&#xff0c;对于二元特征发烧(Fever)和咳嗽(Cough)&#xff0c;模型A只是一个简单的&#34;和&#34;函数&#xff0c;模型B是相同的函数&#xff0c;但是当为咳嗽时预测值会增加&#xff08;加10分&#xff09;&#xff0c;使得模型更依赖于咳嗽&#xff0c;这时因咳嗽更重要&#xff0c;导致在模型B中咳嗽先分裂。</p> 
<p>比较A、B模型在下面六种归因方法上的差别&#xff1a;</p> 
<ul><li>Tree SHAP&#xff0c;本文提出的一种新的个性化方法。&#xff08;个性化特征归因方法&#xff0c;为单个预测计算&#xff09;</li><li>Saabas&#xff0c;个性化的启发式特征归因方法。&#xff08;个性化特征归因方法&#xff0c;为单个预测计算&#xff09;</li><li>mean&#xff08;|Tree SHAP |&#xff09;&#xff0c;基于个性化Tree SHAP归因的平均幅度的全局归因方法&#xff08;全局特征归因方法&#xff0c;为整个数据集计算&#xff0c;实际为所有样本的Tree SHAP值按照特征计算均值&#xff09;</li><li>增益&#xff08;全局特征归因方法&#xff0c;为整个数据集计算&#xff09;</li><li>分裂数&#xff08;全局特征归因方法&#xff0c;为整个数据集计算&#xff09;</li><li>置换&#xff08;全局特征归因方法&#xff0c;为整个数据集计算&#xff09;</li></ul> 
<p><img src="https://img-blog.csdnimg.cn/20210413172245504.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /></p> 
<p>个性化特征归因方法&#xff1a;Tree SHAP、Sabbas&#xff0c;只有SHAP值能够保证反映特征的重要性&#xff0c;而Saabas值可能会给出错误的结果&#xff0c;比如模型B中认为更大的原因是发烧&#xff0c;而不是咳嗽&#xff0c;这是不一致的表现。</p> 
<p>全局特征归因方法&#xff1a;mean&#xff08;|Tree SHAP |&#xff09;、增益、分裂数和特征置换&#xff0c;只有mean&#xff08;|Tree SHAP |&#xff09;和置换认为模型B咳嗽比发烧更重要&#xff0c;这意味着在一致性上增益和分裂数不是全局特性重要性的可靠度量。</p> 
<p>所以gain、split count和Saabas方法中的特征重要度都不一致&#xff08;使B模型更加依赖咳嗽时&#xff0c;却认为发烧更重要&#xff09;&#xff0c;这意味着模型改变为更多地依赖于给定的特性时&#xff0c;分配给该特征的重要性却降低了。通常我们期望树根附近的特征比在叶子附近分裂的特征更重要&#xff08;因为树是贪婪地构造的&#xff09;&#xff0c;然而增益方法偏向于更重视较低的分裂&#xff0c;这种偏差会导致不一致&#xff0c;当咳嗽变得更加重要时&#xff08;因此在根部分裂&#xff09;&#xff0c;其归因重要性实际上下降。个性化的Saabas方法在我们下降树时计算预测的差异&#xff0c;因此它也会受到与树中较低分割相同的偏差&#xff0c;随着树木越来越深&#xff0c;这种偏差只会增长。 相比之下&#xff0c;Tree SHAP方法在数学上等效于平均所有可能的特征排序的预测差异&#xff0c;而不仅仅是它们在树中的位置指定的排序。</p> 
<p>所以在我们考虑的方法中&#xff0c;<strong>只有SHAP值和置换的方法是具有一致性的</strong>&#xff0c;而其中又只有SHAP值是个性化的&#xff0c;所以SHAP值是唯一一致的个性化特征归因方法。</p> 
<hr /> 
<h2><a id="5_base_values__shap_522"></a>5 详解base_values 和 单样本shap值的计算过程</h2> 
<p>有一小部分参考&#xff1a;<a href="https://zhuanlan.zhihu.com/p/106320452">不再黑盒&#xff0c;机器学习解释利器&#xff1a;SHAP原理及实战</a><br /> 主要参考&#xff1a;<br /> <a href="https://github.com/slundberg/shap/blob/9411b68e8057a6c6f3621765b89b24d82bee13d4/tests/explainers/test_linear.py">slundberg/shap/test_linear.py</a></p> 
<pre><code>import xgboost
import shap
shap.initjs()

# train an XGBoost model
X, y &#61; shap.datasets.boston()
model &#61; xgboost.XGBRegressor().fit(X, y)

# explain the model&#39;s predictions using SHAP
# (same syntax works for LightGBM, CatBoost, scikit-learn, transformers, Spark, etc.)
explainer &#61; shap.Explainer(model)
shap_values &#61; explainer(X)

# visualize the first prediction&#39;s explanation
shap.plots.waterfall(shap_values[0])


# 第0个样本
# 样本 x 的所有特征的贡献之和等于预测值减去平均预测值
shap_values[0].base_values
shap_values[0].values
shap_values[0].data


predict &#61; model.predict(pd.DataFrame(X.iloc[0,:]).T)
# base_values -&gt; 平均&#xff0c;f(x) 值 所有样本一样
# values -&gt; 这一个样本&#xff0c;每个特征的SHAP值
# data -&gt; 这一个样本&#xff0c;样本特征值

# 每个样本特征shap值 与模型预测值的差异
sum(shap_values[0].values) &#61; predict - shap_values[0].base_values

# 平均base_values的计算来源
model.predict(X).mean()
shap_values[0].base_values
</code></pre> 
<p>每个样本特征shap值之和 &#61; 该样本模型预测值 - 所有样本预测值的平均值 &#61; <code>predict - shap_values[0].base_values</code><br /> base_values &#61; 模型预测值平均值 &#61; <code>model.predict(X).mean()</code></p> 
<p>就是计算每一个特征的shapley值之和&#xff0c;就是 <code>整体 偏离平均预测值的贡献</code></p> 
<p>在文献<a href="https://zhuanlan.zhihu.com/p/106320452">不再黑盒&#xff0c;机器学习解释利器&#xff1a;SHAP原理及实战</a>提到的是&#xff0c;貌似现在shap版本暂时没有做对数化处理&#xff1a;<img src="https://img-blog.csdnimg.cn/1ef4296642ac4f6783a29a6adc26023c.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /></p> 
<h2><a id="6__571"></a>6 其他细节的延申</h2> 
<h3><a id="61_waterfall____572"></a>6.1 waterfall图&#xff0c;从只能画一人 -&gt; 支持多人</h3> 
<pre><code>import xgboost
import shap

# train an XGBoost model
X, y &#61; shap.datasets.boston()
model &#61; xgboost.XGBRegressor().fit(X, y)

# explain the model&#39;s predictions using SHAP
# (same syntax works for LightGBM, CatBoost, scikit-learn, transformers, Spark, etc.)
explainer &#61; shap.Explainer(model)
shap_values &#61; explainer(X)

# visualize the first prediction&#39;s explanation
shap.plots.waterfall(shap_values[0])
</code></pre> 
<p>一个简单案例中提及如上&#xff0c;可以看到<code>waterfall</code>支持一个人&#xff0c;而且<code>shap_values</code>是一个比较特殊的类&#xff0c;会必须要<code>data</code>和<code>values</code>和<code>base_values</code><br /> 但是多个人要画这个图&#xff0c;这里可以自己将shap值进行相加/求平均&#xff0c;<br /> 然后得到多人的特征&#xff0c;自己DIY一下&#xff1a;</p> 
<pre><code>class new_shap_values():
    def __init__(self,shap_values,bool_tf &#61; None,method &#61; &#39;sum&#39;):
        self.feature_names &#61; shap_values.feature_names
        if method &#61;&#61; &#39;sum&#39;:
            self.data &#61; np.nansum(shap_values.data[bool_tf], axis&#61;0)
            self.values &#61; np.nansum(shap_values.values[bool_tf], axis&#61;0)
            self.base_values &#61; np.nansum(shap_values.base_values[bool_tf], axis&#61;0)
        elif method &#61;&#61; &#39;mean&#39;:
            self.data &#61; np.nanmean(shap_values.data[bool_tf], axis&#61;0)
            self.values &#61; np.nanmean(shap_values.values[bool_tf], axis&#61;0)
            self.base_values &#61; np.nanmean(shap_values.base_values[bool_tf], axis&#61;0)
        else:
            print(&#39;sry,not right method.&#39;)

shap.plots.waterfall(new_shap_values(shap_values,bool_tf &#61; X[&#39;CRIM&#39;] &gt; 10,method &#61; &#39;sum&#39;))
</code></pre> 
<p><code>bool_tf</code>是可以灵活选中</p> 
<h3><a id="62_shap___612"></a>6.2 一个完整的shap值重要性输出 案例</h3> 
<pre><code>import xgboost
import shap
shap.initjs()

# train an XGBoost model
X, y &#61; shap.datasets.boston()
model &#61; xgboost.XGBRegressor().fit(X, y)
</code></pre> 
<p>在fit的过程中如果报错&#xff1a;</p> 
<pre><code>ValueError: DataFrame.dtypes for data must be int, float, bool or categorical.  When
categorical type is supplied, DMatrix parameter
&#96;enable_categorical&#96; must be set to &#96;True&#96;.feat1, feat12, feat123, feat1234
</code></pre> 
<p>那么可以使用<code>_get_numeric_data</code>&#xff1a;</p> 
<pre><code>model &#61; xgboost.XGBRegressor().fit(X._get_numeric_data(), y)
</code></pre> 
<p>此时可以输出xgb模型的重要性&#xff1a;</p> 
<pre><code>pd.DataFrame(zip(model.get_booster().feature_names,model.feature_importances_),columns &#61; [&#39;特征名&#39;,&#39;xgb重要性&#39;])
</code></pre> 
<p>然后计算shap值&#xff1a;</p> 
<pre><code># explain the model&#39;s predictions using SHAP
# (same syntax works for LightGBM, CatBoost, scikit-learn, transformers, Spark, etc.)
explainer &#61; shap.Explainer(model)
shap_values &#61; explainer(X)


pd.DataFrame(zip(shap_values.feature_names,np.abs(shap_values.values).mean(0)),columns &#61; [&#39;特征&#39;,&#39;ds重要性&#39;])

</code></pre> 
<p>此时输出了shap值得重要性&#xff0c;其中可以看到重要性得计算方式是<code>np.abs(shap_values.values).mean(0))</code>绝对值求平均得到的<br /> 另外还有一种重要性就是shap值&#xff0c;</p> 
<pre><code>shap_values &#61; pd.DataFrame(shap_values.values,columns &#61; shap_values.feature_names)
</code></pre> 
<p>之前重要性是取绝对值&#xff0c;但是shap是有正负属性的&#xff0c;所以np.sum也可以反应&#xff0c;且带有正负属性<br /> <img src="https://img-blog.csdnimg.cn/de649a45132f457e997a1e1c90163a9e.png?x-oss-process&#61;image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI2OTE3Mzgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述" /></p> 
<h3><a id="63__658"></a>6.3 画图支持中文</h3> 
<p>window版本&#xff1a;</p> 
<pre><code>from matplotlib import pyplot as plt
#解决中文显示问题
plt.rcParams[&#39;font.sans-serif&#39;]&#61;[&#39;SimHei&#39;]
plt.rcParams[&#39;axes.unicode_minus&#39;] &#61; False
</code></pre> 
<p>mac版本&#xff1a;</p> 
<pre><code>import numpy as np
import matplotlib.pyplot as plt
plt.rcParams[&#39;font.sans-serif&#39;] &#61; [&#39;Arial Unicode MS&#39;]
</code></pre>
                </div>
                <link href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/editerView/markdown_views-98b95bb57c.css" rel="stylesheet">
                <link href="https://csdnimg.cn/release/blogv2/dist/mdeditor/css/style-c216769e99.css" rel="stylesheet">
        </div>
        <div id="treeSkill"></div>
    </article>
    
&nbsp;

## refrences
[机器学习模型可解释性进行到底 —— SHAP值理论（一）](https://blog.csdn.net/sinat_26917383/article/details/115400327)  
[SHAP知识点全汇总](https://zhuanlan.zhihu.com/p/85791430)
