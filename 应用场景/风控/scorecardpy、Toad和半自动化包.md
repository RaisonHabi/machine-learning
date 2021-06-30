## 评分卡半自动化建模包
[Scorecard--Function](https://github.com/taenggu0309/Scorecard--Function)

&nbsp;
## 一、toad
方法简介：
```
数据探索
toad.detector.detect(): 返回数据类型，分布，缺失率，异常值比例

特征筛选，细分类，woe编码转换
toad.selection.select()
toad.quality(dataframe, target)：输出值
toad.transform.Combiner()：类别合并
```

信贷评分卡中常用的两个库有scorecardpy和Toad。其中scorecardpy是由谢士晨博士开发，Toad是由厚本金融风控团队内部孵化产生的标准评分卡库。本文以Toad官方指导教程为例，讲解如何运用Toad库进行傻瓜式的评分卡开发。

### Toad基础教程
参考自Toad库中的Basic Tutorial for Toad.

Toad遵循信用风险记分卡模型开发的一般流程:
```
(1)EDA
(2)特征的选择与WOE分箱的结合
(3)模型选择
(4)模型验证
(5)评分卡转换
```
### 1.EDA数据处理
Toad支持常用的特征EDA分析，包括检测缺失值和特征分布。

toad.detector.detect():返回每个特性的EDA报告，包括数据类型、分布、缺失率和惟一值。
```
toad.detector.detect(data_tr).head(10)
```
### 2.特征选择，WOE转换
Toad可以用来过滤大量的特征，如高缺失率、低iv和高度相关的特征。它还可以使用各种分箱技巧进行分箱和实现WOE转化。

#### 2.1 toad.selection.select():用于根据缺失百分比、iv(20箱)和多重共线性(带有VIF和/或自相关)来过滤特征。
```
下面以缺失率大于0.5.IV值小于0.05或者相关性大于0.7(保留较高的特征)来进行特征筛选。

selected_data, drop_lst= toad.selection.select(data_tr,target = 'creditability', empty = 0.5, iv = 0.05,\
              corr = 0.7, return_drop=True, exclude=['type'])

selected_test = data_ts[selected_data.columns]

print(selected_data.shape)
drop_lst
```
#### 2.2 toad.quality(dataframe, target):返回每个特征的质量，包括iv、基尼系数和熵。可以帮助我们发现更有用的潜在信息。
```
quality = toad.quality(data,'creditability')
quality.head(6)
```
### 3.分箱
#### 3.1  分箱
toad.transform.Combiner():可以用来对数值型和类别型变量进行分箱，支持决策树分箱、卡方分箱、最优分箱等。  
该行代码生成一个Combiner类，这个类有以下方法：
```
combiner().fit(data, y = 'target', method = 'chi', min_samples = None, n_bins = None ):分箱，支持卡方、决策树、等频和等宽。
combiner().set_rules(dict):设置箱号。
combiner().transform(data): 将特征的值转化为分箱的箱号。
```
```
# 初始化一个combiner类
combiner = toad.transform.Combiner()

# 训练数据并指定分箱方法，其它参数可选
combiner.fit(selected_data,y='creditability',method='chi',min_samples =  0.05,exclude='type')

# 以字典形式保存分箱结果
bins = combiner.export()

#查看每个特征的分箱结果
print('credit.amount:',bins['credit.amount'])
```
#### 3.2 WOE分箱可视化
```
toad.plot.badrate_plot(data,target = 'target', x = None, by = None): 画出不同数据集的每一箱的bad_rate图。
这里可以是训练集测试集，也可以不同月份的对比。by后面是纵轴。x是需要对比的维度，比如训练集测试集、不同的月份。

toad.plot.proportion_plot(datacol): 画出一个特征每一箱的比例。
```
#### 3.3 分箱转化
```
toad.transform.WOETransformer():对分箱后的数据进行WOE转化
WOETransformer().fit_transform(data, y_true, exclude = None)：
```
### 4.模型选择
toad.selection.stepwise():可以通过向前、向后、双向选择来进行特征选择，使用AIC/BIC/KS/AUC作为选择标准。
```
final_data = toad.selection.stepwise(data_tr_woe.drop('type',axis=1),target = 'creditability',direction = 'both', criterion = 'aic')

final_test = data_ts_woe[final_data.columns]
print(final_data.shape)
print(final_data.columns)
```
可以看到经过模型选择，15个特征减少为8个特征。
### 5.建模
```
Xtr = final_data.drop('creditability',axis=1)
Ytr = final_data['creditability']
Xts = final_test.drop('creditability',axis=1)
Yts = final_test['creditability']

lr = LogisticRegression()
lr.fit(Xtr, Ytr)
```
### 6.模型评估和验证
#### 6.1 常用的评估指标如KS、F1、AUC
```
from toad.metrics import KS, F1, AUC

EYtr_proba = lr.predict_proba(Xtr)[:,1]
EYtr = lr.predict(Xtr)

print('Training error')
print('F1:', F1(EYtr_proba,Ytr))
print('KS:', KS(EYtr_proba,Ytr))
print('AUC:', AUC(EYtr_proba,Ytr))

EYts_proba = lr.predict_proba(Xts)[:,1]
EYts = lr.predict(Xts)

print('\nTest error')
print('F1:', F1(EYts_proba,Yts))
print('KS:', KS(EYts_proba,Yts))
print('AUC:', AUC(EYts_proba,Yts))
```
#### 6.2 PSI 比较训练集和测试集各变量的稳定性。
```
psi = toad.metrics.PSI(final_data,final_test)
psi.sort_values(0,ascending=False) # Further tune the unstable feature if any
```
#### 6.3 KS
```
tr_bucket = toad.metrics.KS_bucket(EYtr_proba,Ytr,bucket=10,method='quantile')
tr_bucket
```
### 7.分数转换
```
card = toad.scorecard.ScoreCard(combiner = combiner, transer = transer , C = 0.1)
card.fit(Xtr, Ytr)
card.export(to_frame = True,).head(10)
```
### Toad库相关的文档：

github主页:
https://github.com/amphibian-dev/toad
文档：
https://toad.readthedocs.io
中文文档：https://toad.readthedocs.io/en/dev/tutorial_chinese.html
演示：https://toad.readthedocs.io/en/latest/tutorial.html

whl下载地址：
https://pypi.org/simple/toad/

&nbsp;
##  二、scorecardpy库
### 1、R:scorecard库
#### 1)var_filter
**Variable Filter**  
This function filter variables base on the specified conditions, **including minimum iv, maximum na percentage and maximum element percentage**.

**Arguments**  
```
dt: A data frame with both x (predictor/feature) and y (response/label) variables.
y: Name of y variable.
x: Name of x variables. Default NULL If x is NULL, all variables exclude y will counted as x variables.

iv_limit: The minimum IV of each kept variable, default 0.02.
na_perc_limit: The maximum rate of NAs in each kept variable, default 0.95.
ele_perc_limit: The maximum rate of identical value (excluding NAs) in each kept variable, default 0.95.

var_rm: Name vector of force removed variables, default NULL.
var_kp :Name vector of force kept variables, default NULL.
return_rm_reason: Logical, default FALSE.
positive: Value of positive class, default "bad|1".
```
**Value**  
A data.table with y and selected x variables and a data.table with the reason of removed x variable if return_rm_reason == TRUE.

[var_filter](https://www.rdocumentation.org/packages/scorecard/versions/0.1.4/topics/var_filter)
#### 2)woebin
**WOE Binning**  
woebin generates optimal binning for both numerical and categorical variables using tree-like segmentation. For the categorical variables, the binning segmentation will ordered by the levels for factor and by the bad probability for character. woebin can also customizing breakpoints for both numerical and categorical variables.

**Arguments**
```
dt: A data frame with both x (predictor/feature) and y (response/label) variables.
y: Name of y variable.
x: Name of x variables. Default NULL If x is NULL, all variables exclude y will counted as x variables.

breaks_list: List of break points, defaults NULL If it is not NULL, variable binning will based on the provided breaks.

min_perc_total: The share of initial binning class number over total. Accepted range: 0.01-0.2; default 0.02.

stop_limit: Stop binning segmentation when information value gain ratio less than the stop_limit. Accepted range: 0-0.5; default 0.1.

max_bin_num: Integer. The maximum binning number.

positive: Value of positive class, default "bad|1".
print_step: A non-negative integer. Default is 1. Print variable names by print_step when print_step>0. If print_step=0, no message is printed.
```
**Value** Optimal or customized binning information

[woebin](https://www.rdocumentation.org/packages/scorecard/versions/0.1.4/topics/woebin)


&nbsp;
### 2、python:scorecardpy库
<p> 　Python中信贷评分卡中常用的两个库有scorecardpy和Toad。其中scorecardpy是由谢士晨博士开发，Toad是由厚本金融风控团队内部孵化产生的标准评分卡库。关于Toad的使用，之前已经写过学习教程，<b><a href="https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/aiYFKi_2gDnVc4-exQjeTQ" class=" wrap external" target="_blank" rel="nofollow noreferrer">Toad库使用教程</a></b>，今天学习一下scorecardpy库的使用。github链接见： <b><a href="https://link.zhihu.com/?target=https%3A//github.com/ShichenXie/scorecardpy" class=" wrap external" target="_blank" rel="nofollow noreferrer">scorecardpy</a></b></p><p> 　该软件包是R软件包评分卡的python版本。它的目标是通过提供一些常见任务的功能，使传统信用风险计分卡模型的开发更加轻松有效。该包的功能及对应的函数如下：</p><ul><li>数据划分(split_df)</li><li>变量选择(iv, var_filter)</li><li>变量分箱(woebin, woebin_plot, woebin_adj, woebin_ply)</li><li>评分转换(scorecard, scorecard_ply)</li><li>模型评估(perf_eva, perf_psi)</li></ul><p> 　首先，导入germancredit数据。</p><div class="highlight"><pre><code class="language-text">import scorecardpy as sc
dat = sc.germancredit()

dt_s = sc.var_filter(dat, y=&#34;creditability&#34;)
</code></pre></div><p> 　这个函数可以根据指定的条件筛选变量，例如IV值、缺失率、一致性等，函数的参数如下:</p><div class="highlight"><pre><code class="language-text">def var_filter(dt, y, x=None, iv_limit=0.02, missing_limit=0.95, 
               identical_limit=0.95, var_rm=None, var_kp=None, 
               return_rm_reason=False, positive=&#39;bad|1&#39;)
</code></pre></div><blockquote> var_rm：强制删除变量的名称<br/>var_kp：强制保留变量的名称<br/>return_rm_reason：是否返回每个变量被删除的原因<br/>positive:坏样本的标签<br/> </blockquote><h3><b>数据划分</b></h3><div class="highlight"><pre><code class="language-text">train, test = sc.split_df(dt_s, &#39;creditability&#39;).values()

def split_df(dt, y=None, ratio=0.7, seed=186)
</code></pre></div><p> 　该函数的ratio默认为0.7，即按照7:3对数据集进行分割。ratio可以随意进行设置，比如[0.5,0.2]</p><h3><b>变量分箱</b></h3><div class="highlight"><pre><code class="language-text">bins = sc.woebin(dt_s, y=&#34;creditability&#34;)

def woebin(dt, y, x=None, 
           var_skip=None, breaks_list=None, special_values=None, 
           stop_limit=0.1, count_distr_limit=0.05, bin_num_limit=8, 
           # min_perc_fine_bin=0.02, min_perc_coarse_bin=0.05, max_num_bin=8, 
           positive=&#34;bad|1&#34;, no_cores=None, print_step=0, method=&#34;tree&#34;,
           ignore_const_cols=True, ignore_datetime_cols=True, 
           check_cate_num=True, replace_blank=True, 
           save_breaks_list=None, **kwargs):
</code></pre></div><p> 　返回的是每个变量的分箱结果组成的字典。 </p><figure data-size="normal"><noscript><img src="https://pic3.zhimg.com/v2-2117b40c936a32c3d50e8dc7a300ad86_b.jpg" data-caption="" data-size="normal" data-rawwidth="930" data-rawheight="172" class="origin_image zh-lightbox-thumb" width="930" data-original="https://pic3.zhimg.com/v2-2117b40c936a32c3d50e8dc7a300ad86_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;930&#39; height=&#39;172&#39;&gt;&lt;/svg&gt;" data-caption="" data-size="normal" data-rawwidth="930" data-rawheight="172" class="origin_image zh-lightbox-thumb lazy" width="930" data-original="https://pic3.zhimg.com/v2-2117b40c936a32c3d50e8dc7a300ad86_r.jpg" data-actualsrc="https://pic3.zhimg.com/v2-2117b40c936a32c3d50e8dc7a300ad86_b.jpg"/></figure><p class="ztext-empty-paragraph"><br/></p><p> 　woebin支持决策树分箱、卡方分箱、自定义分箱，默认的WOE值计算是用坏样本率/好样本率，这个可以通过参数postive进行调整。如果某一箱只有好样本或者坏样本，会对缺失的类别赋予0.99进行调整，方便计算woe值。重要参数含义如下：</p><blockquote> var_skip:指定不需要分箱的变量。<br/>breaks_list:分割点的List。对分箱进行调整的时候用。可以进行自定义分箱<br/>special_values:指定单独的箱。<br/>count_distr_limit:分箱结果中最小占比。默认0.05<br/>stop_limit:当IV值的增加值小于stop_limit或者卡方值小于qchisq(1-stoplimit, 1)时停止分割。<br/>bin_num_limit:最大分箱数。<br/>method:分箱方法，可以有&#34;tree&#34; or &#34;chimerge&#34;。<br/>ignore_const_cols:是否忽略常数列。<br/>check_cate_num:检查分类变量中类别数是否大于50。<br/>replace_blank:将空值替换为None。<br/> </blockquote><p> 　sc.woebin_plot()可以画出变量分箱之后的Bi_variate图，这里的坏样本率图展示了每一箱的好坏样本数、样本占比、坏样本率，比较清晰明了。 </p><figure data-size="normal"><noscript><img src="https://pic1.zhimg.com/v2-d63cf039efa46d7823f2d58773c2e72a_b.jpg" data-caption="" data-size="normal" data-rawwidth="435" data-rawheight="276" class="origin_image zh-lightbox-thumb" width="435" data-original="https://pic1.zhimg.com/v2-d63cf039efa46d7823f2d58773c2e72a_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;435&#39; height=&#39;276&#39;&gt;&lt;/svg&gt;" data-caption="" data-size="normal" data-rawwidth="435" data-rawheight="276" class="origin_image zh-lightbox-thumb lazy" width="435" data-original="https://pic1.zhimg.com/v2-d63cf039efa46d7823f2d58773c2e72a_r.jpg" data-actualsrc="https://pic1.zhimg.com/v2-d63cf039efa46d7823f2d58773c2e72a_b.jpg"/></figure><p class="ztext-empty-paragraph"><br/></p><div class="highlight"><pre><code class="language-text">sc.woebin_plot(bins)
</code></pre></div><p>分箱调整</p><div class="highlight"><pre><code class="language-text">breaks_adj = sc.woebin_adj(dt_s, &#34;creditability&#34;, bins) 

def woebin_adj(dt, y, bins, adj_all_var=False, special_values=None, method=&#34;tree&#34;, save_breaks_list=None, count_distr_limit=0.05)
</code></pre></div><p>重要参数的含义：</p><blockquote> adj_all_var:是否显示woe变量的单调性。<br/> </blockquote><p> 　其它参数和woebin()函数一样，这里没有深入研读调整分箱的代码，而且运行过程中有报错。猜测调整的方向是坏样本率单调。<br/> 　此外也可以手动进行分箱调整：</p><div class="highlight"><pre><code class="language-text">breaks_adj = {
    &#39;age.in.years&#39;: [26, 35, 40],
    &#39;other.debtors.or.guarantors&#39;: [&#34;none&#34;, &#34;co-applicant%,%guarantor&#34;]
}
bins_adj = sc.woebin(dt_s, y=&#34;creditability&#34;, breaks_list=breaks_adj)
</code></pre></div><h3><b>woe转换</b></h3><p> 　分箱之后需要对变量的原始值进行转换，将变量值转化成woe值，后续使用变量的WOE值入模进行训练。</p><div class="highlight"><pre><code class="language-text">train_woe = sc.woebin_ply(train, bins_adj)
test_woe = sc.woebin_ply(test, bins_adj)
</code></pre></div><h3><b>模型建立</b></h3><div class="highlight"><pre><code class="language-text">y_train = train_woe.loc[:,&#39;creditability&#39;]
X_train = train_woe.loc[:,train_woe.columns != &#39;creditability&#39;]
y_test = test_woe.loc[:,&#39;creditability&#39;]
X_test = test_woe.loc[:,train_woe.columns != &#39;creditability&#39;]

#logistic regression ------
from sklearn.linear_model import LogisticRegression
lr = LogisticRegression(penalty=&#39;l1&#39;, C=0.9, solver=&#39;saga&#39;, n_jobs=-1)
lr.fit(X_train, y_train)
#lr.coef_
#lr.intercept_

#predicted proability
train_pred = lr.predict_proba(X_train)[:,1]
test_pred = lr.predict_proba(X_test)[:,1]
</code></pre></div><h3><b>模型评估</b></h3><div class="highlight"><pre><code class="language-text">train_perf = sc.perf_eva(y_train, train_pred, title = &#34;train&#34;)
test_perf = sc.perf_eva(y_test, test_pred, title = &#34;test&#34;)
</code></pre></div><figure data-size="normal"><noscript><img src="https://pic4.zhimg.com/v2-1ed31dc2ebee533f6f6915bc5dcb8932_b.jpg" data-caption="" data-size="normal" data-rawwidth="394" data-rawheight="440" class="content_image" width="394"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;394&#39; height=&#39;440&#39;&gt;&lt;/svg&gt;" data-caption="" data-size="normal" data-rawwidth="394" data-rawheight="440" class="content_image lazy" width="394" data-actualsrc="https://pic4.zhimg.com/v2-1ed31dc2ebee533f6f6915bc5dcb8932_b.jpg"/></figure><div class="highlight"><pre><code class="language-text">def perf_eva(label, pred, title=None, groupnum=None, plot_type=[&#34;ks&#34;, &#34;roc&#34;], show_plot=True, positive=&#34;bad|1&#34;, seed=186)
</code></pre></div><p> 　perf_eva()函数可以从KS、AUC、Lift曲线、PR曲线评估模型的效果。可以通过plot_type参数控制评估方法，可以选&#34;ks&#34;, &#34;lift&#34;, &#34;roc&#34;, &#34;pr&#34;。s</p><h3><b>评分映射</b></h3><p> 　模型评估之后，需要对概率进行映射，转换成评分卡得分。得分包括每个客户的最终得分和单个变量的得分。</p><div class="highlight"><pre><code class="language-text">card = sc.scorecard(bins_adj, lr, X_train.columns)
</code></pre></div><p> 　scorecard()返回一个字典，对应的是基础分和每个变量的得分。 </p><figure data-size="normal"><noscript><img src="https://picb.zhimg.com/v2-5db3f57e721e4d1c912851565622d437_b.jpg" data-caption="" data-size="normal" data-rawwidth="501" data-rawheight="162" class="origin_image zh-lightbox-thumb" width="501" data-original="https://picb.zhimg.com/v2-5db3f57e721e4d1c912851565622d437_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;501&#39; height=&#39;162&#39;&gt;&lt;/svg&gt;" data-caption="" data-size="normal" data-rawwidth="501" data-rawheight="162" class="origin_image zh-lightbox-thumb lazy" width="501" data-original="https://picb.zhimg.com/v2-5db3f57e721e4d1c912851565622d437_r.jpg" data-actualsrc="https://picb.zhimg.com/v2-5db3f57e721e4d1c912851565622d437_b.jpg"/></figure><p class="ztext-empty-paragraph"><br/></p><div class="highlight"><pre><code class="language-text">def scorecard(bins, model, xcolumns, points0=600, odds0=1/19, pdo=50, basepoints_eq0=False)
</code></pre></div><p> 　scorecard()函数的参数含义如下：</p><blockquote> bins:分箱信息。woebin()返回的结果。<br/>model:模型对象。<br/>points0:基础分，默认为600。 odds:好坏比，默认为1:19<br/>pdo:比率翻番的倍数，默认为50。<br/>basepoints_eq0:如果为True,则将基础分分散到每个变量中。<br/> </blockquote><p>评分稳定性评估--PSI</p><div class="highlight"><pre><code class="language-text">sc.perf_psi(
  score = {&#39;train&#39;:train_score, &#39;test&#39;:test_score},
  label = {&#39;train&#39;:y_train, &#39;test&#39;:y_test}
)
</code></pre></div><figure data-size="normal"><noscript><img src="https://pic3.zhimg.com/v2-629e4c0886656a692d94a6c49af788e5_b.jpg" data-caption="" data-size="normal" data-rawwidth="587" data-rawheight="336" class="origin_image zh-lightbox-thumb" width="587" data-original="https://pic3.zhimg.com/v2-629e4c0886656a692d94a6c49af788e5_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;587&#39; height=&#39;336&#39;&gt;&lt;/svg&gt;" data-caption="" data-size="normal" data-rawwidth="587" data-rawheight="336" class="origin_image zh-lightbox-thumb lazy" width="587" data-original="https://pic3.zhimg.com/v2-629e4c0886656a692d94a6c49af788e5_r.jpg" data-actualsrc="https://pic3.zhimg.com/v2-629e4c0886656a692d94a6c49af788e5_b.jpg"/></figure><p><b>总结</b>:对于想学习建模的新手来说，一套有效的脚本能够帮助快速建立一张评分卡。本人在自学的过程中看了几乎网上所有能找到的评分卡脚本，比较实用的还是之前github上的半自动建模包，自己也尝试写了一个评分卡脚本，终于算是入门完成。scorecardpy库虽然封装得很好，但是一旦运行过程中出现问题，调试起来仍然是件很麻烦的事情。对比起来，本人觉得半自动化建模的包更具有实用性，将链接附在下方，感兴趣者可以学习。 <b><a href="https://link.zhihu.com/?target=https%3A//github.com/taenggu0309/Scorecard--Function" class=" wrap external" target="_blank" rel="nofollow noreferrer">建立一个评分卡模型的代码包</a></b></p>

&nbsp;
## References
[scorecardpy库的使用简介](https://zhuanlan.zhihu.com/p/150149761)  
[傻瓜式评分卡建模库Toad使用](https://mp.weixin.qq.com/s/aiYFKi_2gDnVc4-exQjeTQ)  
[scorecardpy](https://pypi.org/project/scorecardpy/)  
[toad使用教程](https://toad.readthedocs.io/en/dev/tutorial_chinese.html)
