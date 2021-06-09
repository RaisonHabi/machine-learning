<h2><b>风控业务背景</b></h2><p>在风控中，<b>稳定性压倒一切</b>。原因在于，一套风控模型正式上线运行后往往需要很久（通常一年以上）才会被替换下线。如果模型不稳定，意味着模型不可控，对于业务本身而言就是一种不确定性风险，直接影响决策的合理性。这是不可接受的。</p><p>本文将从稳定性的直观理解、群体稳定性指标（Population Stability Index，PSI）的计算逻辑、PSI背后的含义等多维度展开分析。</p><blockquote><b>目录</b><br/>Part 1. 稳定性的直观理解<br/>Part 2. 群体稳定性指标（Population Stability Index，PSI）的理解<br/>Part 3. 相对熵（KL散度）的理解<br/>Part 4. 相对熵与PSI之间的关系<br/>Part 5. PSI指标的业务应用<br/>Part 6. PSI的计算代码（Python）<br/>致谢<br/>版权声明<br/>参考资料</blockquote><h2><b>Part 1. 稳定性的直观理解</b></h2><p>在日常生活中，我们可能会看到每月电表、水表数值的变化。直观理解上的系统稳定，通常是指<b>某项指标波动小（低方差</b>），指标曲线几乎是一条水平的直线。此时，我们就会觉得系统运行正常稳定，很有安全感。</p><p>在数学上，我们通常可以用<b>变异系数（</b>Coefficient of Variation，CV<b>）</b>来衡量这种数据波动水平。变异系数越小，代表波动越小，稳定性越好。</p><blockquote>变异系数的计算公式为：变异系数 C·V =（ 标准偏差 SD / 平均值Mean ）× 100%</blockquote><p>那么，是不是只用用变异系数就可以了呢？方便、直观。——答案是否定的。在机器学习建模时，我们基于假设“<b>历史样本分布等于未来样本分布</b>”。因此，我们通常认为：</p><blockquote>模型或变量稳定 &lt;=&gt; 未来样本分布与历史样本分布之间的偏差小。</blockquote><p>然而，实际中由于受到客群变化（互金市场用户群体变化快）、数据源采集变化（比如爬虫接口被风控了）等等因素影响，实际样本分布将会发生偏移，就会导致模型不稳定。</p><h2><b>Part 2. 群体稳定性指标（Population Stability Index，PSI）的理解</b></h2><p>如果你有基础的风控建模经验，想必会很熟悉PSI（Population Stability Index）指标。PSI反映了<b>验证样本</b>在各分数段的分布与<b>建模样本</b>分布的稳定性。在建模中，我们常用来<b>筛选特征变量、评估模型稳定性</b>。</p><p>那么，PSI的计算逻辑是怎样的呢？很多博客文章都会直接告诉我们，<b>稳定性是有参照的</b>，因此<b>需要有两个分布——实际分布</b>（actual）<b>和预期分布</b>（expected）。其中，在建模时通常以训练样本（In the Sample, INS）作为预期分布，而验证样本通常作为实际分布。验证样本一般包括样本外（Out of Sample，OOS）和跨时间样本（Out of Time，OOT）。</p><p>我们从直觉上理解，是不是可以<b>把两个分布重叠放在一起，比较下两个分布的差异有多大</b>？</p><figure data-size="normal"><noscript><img src="https://pic3.zhimg.com/v2-2e0a202197517e22ff21280e324f36ad_b.jpg" data-size="normal" data-rawwidth="720" data-rawheight="394" class="origin_image zh-lightbox-thumb" width="720" data-original="https://pic3.zhimg.com/v2-2e0a202197517e22ff21280e324f36ad_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;720&#39; height=&#39;394&#39;&gt;&lt;/svg&gt;" data-size="normal" data-rawwidth="720" data-rawheight="394" class="origin_image zh-lightbox-thumb lazy" width="720" data-original="https://pic3.zhimg.com/v2-2e0a202197517e22ff21280e324f36ad_r.jpg" data-actualsrc="https://pic3.zhimg.com/v2-2e0a202197517e22ff21280e324f36ad_b.jpg"/><figcaption>图 1 - 分数分布差异对比</figcaption></figure><p>PSI的计算公式也告诉我们，的确是这样的：</p><p><img src="https://www.zhihu.com/equation?tex=psi+%3D+%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%7B%28A_i+-+E_i%29%7D+%2A+ln%28A_i+%2F+E_i%29+%5Ctag%7B1%7D" alt="psi = \sum_{i=1}^{n}{(A_i - E_i)} * ln(A_i / E_i) \tag{1}" eeimg="1"/> </p><blockquote><b>                         PSI = SUM( (实际占比 - 预期占比）*  ln(实际占比 / 预期占比) ) </b></blockquote><p>大多数同学到了这里，遵循拿来主义，直接应用。但是，大家有没有思考过以下几个问题：</p><p><b>Q1:</b>  为什么要在计算公式中<b>引入对数—— ln(实际占比 / 预期占比)</b>？</p><p><b>Q2:</b>  为什么log的底数是自然常数e，而不是其他常数（比如2）？（信息论中基常常选择为2，因此信息的单位为<b>比特bits</b>；而机器学习中基常选择为自然常数，因此单位常被称为<b>奈特nats</b>）</p><p><b>Q3</b>:  如果只是衡量两个分布的差异，为什么不直接定义成以下公式呢？这个公式含义是把各个分数段里的人群占比差异进行累加放大。</p><blockquote><b>自定义的稳定性指标  = SUM(实际占比 - 预期占比）</b></blockquote><p>让我们先保留这些疑问，回到PSI的计算公式，以图2表格数据为例介绍下计算步骤。</p><ul><li><b>step1</b>：将<b>变量预期分布</b>（excepted）进行<b>分箱</b>（binning）离散化，统计各个分箱里的样本占比。<br/>注意：<br/>a) 分箱可以是等频、等距或其他方式，分箱方式不同，将导致计算结果略微有差异；<br/>b) 对于<b>连续型</b>变量（特征变量、模型分数等），分箱数需要设置合理，一般设为10或20；对于离散型变量，如果分箱太多可以提前考虑合并小分箱；分箱数太多，可能会导致每个分箱内的样本量太少而失去统计意义；分箱数太少，又会导致计算结果精度降低。</li><li><b>step2</b>: 按相同分箱区间，对<b>实际分布（actual）</b>统计各分箱内的样本占比<b>。</b></li><li><b>step3</b>:计 算各分箱内的<b>A - E</b>和<b>Ln(A / E)</b>，计算<b>index = (实际占比 - 预期占比）*  ln(实际占比 / 预期占比) 。</b></li><li><b>step4</b>: 将各分箱的index进行求和，即得到最终的PSI。</li></ul><figure data-size="normal"><noscript><img src="https://pic1.zhimg.com/v2-41d13c635bdbf91ca084dde9bd2bfe5a_b.jpg" data-size="normal" data-rawwidth="2170" data-rawheight="796" class="origin_image zh-lightbox-thumb" width="2170" data-original="https://pic1.zhimg.com/v2-41d13c635bdbf91ca084dde9bd2bfe5a_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;2170&#39; height=&#39;796&#39;&gt;&lt;/svg&gt;" data-size="normal" data-rawwidth="2170" data-rawheight="796" class="origin_image zh-lightbox-thumb lazy" width="2170" data-original="https://pic1.zhimg.com/v2-41d13c635bdbf91ca084dde9bd2bfe5a_r.jpg" data-actualsrc="https://pic1.zhimg.com/v2-41d13c635bdbf91ca084dde9bd2bfe5a_b.jpg"/><figcaption>图 2 - PSI计算表格</figcaption></figure><p>在计算得到PSI指标后，这个数字又代表什么业务含义呢？<b>PSI数值越小，两个分布之间的差异就越小，代表越稳定。</b></p><figure data-size="normal"><noscript><img src="https://pic4.zhimg.com/v2-df25e14b6c7cfe4f8bd8bae5533dc5f8_b.png" data-size="normal" data-rawwidth="2284" data-rawheight="318" class="origin_image zh-lightbox-thumb" width="2284" data-original="https://pic4.zhimg.com/v2-df25e14b6c7cfe4f8bd8bae5533dc5f8_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;2284&#39; height=&#39;318&#39;&gt;&lt;/svg&gt;" data-size="normal" data-rawwidth="2284" data-rawheight="318" class="origin_image zh-lightbox-thumb lazy" width="2284" data-original="https://pic4.zhimg.com/v2-df25e14b6c7cfe4f8bd8bae5533dc5f8_r.jpg" data-actualsrc="https://pic4.zhimg.com/v2-df25e14b6c7cfe4f8bd8bae5533dc5f8_b.png"/><figcaption>图 3 - PSI数值的业务含义</figcaption></figure><h2><b>Part 3. 相对熵（KL散度）的理解</b></h2><p>相对熵（relative entropy），又被称为Kullback-Leibler散度（Kullback-Leibler divergence）或信息散度（information divergence），是两个概率分布间差异的<b>非对称性</b>度量。</p><p> 划重点——<b>KL散度不满足对称性。</b></p><p>我们先不考虑相对熵的计算逻辑，先看它的物理含义是什么？</p><blockquote> 相对熵可以衡量两个随机分布之间的<b>&#34;距离“</b>。<br/>1）当两个随机分布相同时，它们的<b>相对熵为零</b>；当两个随机分布的差别增大时，它们的相对熵也会增大。<br/>2）注意⚠️：相对熵是一个从<b>信息论角度量化距离</b>的指标，与<b>数学概念上的距离</b>有所差异。数学上的距离需要满足：非负性、<b>对称性</b>、同一性、传递性等；而<b>相对熵不满足对称性</b>。</blockquote><p>看到这里，是不是感觉和PSI的概念非常相似？ </p><blockquote>当两个随机分布完全一样时，PSI = 0；反之，差异越大，PSI越大。</blockquote><p>直觉告诉我们<b>相对熵和PSI应该存在着某种隐含的关系，真相正在慢慢浮出水面。</b></p><p>我们再来看相对熵的计算公式。<b>在信息理论中，相对熵等价于两个概率分布的信息熵（Shannon entropy）的差值。</b></p><p><img src="https://www.zhihu.com/equation?tex=KL%28P%7C%7CQ%29%3D-%5Csum_%7Bx+%5Cin+X%7DP%28x%29log%5Cfrac%7B1%7D%7BP%28x%29%7D+%2B+%5Csum_%7Bx+%5Cin+X%7DP%28x%29log%5Cfrac%7B1%7D%7BQ%28x%29%7D+%5C%5C+%3D+%5Csum_%7Bx+%5Cin+X%7DP%28x%29log%5Cfrac%7BP%28x%29%7D%7BQ%28x%29%7D+%5Ctag%7B2%7D" alt="KL(P||Q)=-\sum_{x \in X}P(x)log\frac{1}{P(x)} + \sum_{x \in X}P(x)log\frac{1}{Q(x)} \\ = \sum_{x \in X}P(x)log\frac{P(x)}{Q(x)} \tag{2}" eeimg="1"/> </p><p>其中，P(x)表示数据的<b>真实分布</b>，而Q(x)表示数据的<b>观察分布</b>。上式可以理解为：</p><blockquote><b>概率分布携带着信息，可以用信息熵来衡量。</b><br/><b>若用观察分布Q(x)来描述真实分布P(x)，还需要多少额外的信息量？</b></blockquote><figure data-size="normal"><noscript><img src="https://pic1.zhimg.com/v2-372b29196647230da027de164c915356_b.png" data-size="normal" data-rawwidth="1400" data-rawheight="218" class="origin_image zh-lightbox-thumb" width="1400" data-original="https://pic1.zhimg.com/v2-372b29196647230da027de164c915356_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;1400&#39; height=&#39;218&#39;&gt;&lt;/svg&gt;" data-size="normal" data-rawwidth="1400" data-rawheight="218" class="origin_image zh-lightbox-thumb lazy" width="1400" data-original="https://pic1.zhimg.com/v2-372b29196647230da027de164c915356_r.jpg" data-actualsrc="https://pic1.zhimg.com/v2-372b29196647230da027de164c915356_b.png"/><figcaption>图 4 - KL散度具有非对称性</figcaption></figure><p>因此，<b>KL散度是单向描述信息熵差异</b>。为了加强大家理解，接下来以数值案例介绍相对熵如何计算。</p><p>假如一个字符发射器，随机发出0和1两种字符，真实发出概率分布为A，但实际不知道A的具体分布。通过观察，得到概率分布B与C，各个分布的具体情况如下：</p><figure data-size="normal"><noscript><img src="https://pic1.zhimg.com/v2-f82e0dadf2bb5d38f80005f5698f31c4_b.jpg" data-size="normal" data-rawwidth="1406" data-rawheight="464" class="origin_image zh-lightbox-thumb" width="1406" data-original="https://pic1.zhimg.com/v2-f82e0dadf2bb5d38f80005f5698f31c4_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;1406&#39; height=&#39;464&#39;&gt;&lt;/svg&gt;" data-size="normal" data-rawwidth="1406" data-rawheight="464" class="origin_image zh-lightbox-thumb lazy" width="1406" data-original="https://pic1.zhimg.com/v2-f82e0dadf2bb5d38f80005f5698f31c4_r.jpg" data-actualsrc="https://pic1.zhimg.com/v2-f82e0dadf2bb5d38f80005f5698f31c4_b.jpg"/><figcaption>图 5 - 三种数据分布</figcaption></figure><p>可以计算出得到KL散度如下：</p><p><img src="https://www.zhihu.com/equation?tex=KL%28A%7C%7CB%29%3D1%2F2log%28%5Cfrac%7B1%2F2%7D%7B1%2F4%7D%29%2B1%2F2log%28%5Cfrac%7B1%2F2%7D%7B3%2F4%7D%3D1%2F2log%284%2F3%29+%5C%5C+KL%28A%7C%7CC%29%3D1%2F2log%28%5Cfrac%7B1%2F2%7D%7B1%2F8%7D%29%2B1%2F2log%28%5Cfrac%7B1%2F2%7D%7B7%2F8%7D%3D1%2F2log%2816%2F7%29+%5Ctag%7B3%7D+" alt="KL(A||B)=1/2log(\frac{1/2}{1/4})+1/2log(\frac{1/2}{3/4}=1/2log(4/3) \\ KL(A||C)=1/2log(\frac{1/2}{1/8})+1/2log(\frac{1/2}{7/8}=1/2log(16/7) \tag{3} " eeimg="1"/> </p><p>由上式可知，相对熵<b>KL(A||C) &gt; KL(A||B)</b>，说明<b>A和B之间的概率分布在信息量角度更为接近。</b>而通过概率分布可视化观察，我们也<b>认为A和B更为接近</b>，两者吻合。</p><h2><b>Part 4. 相对熵与PSI之间的关系</b></h2><p>接下来，我们从数学上来分析相对熵和PSI之间的关系。</p><p><img src="https://www.zhihu.com/equation?tex=psi+%3D+%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%7B%28A_i+-+E_i%29%7D+%2A+ln%28A_i+%2F+E_i%29+%5C%5C+++psi+%3D+%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%7BA_i%7D+%2A+ln%28A_i+%2F+E_i%29++%2B+%5Csum_%7Bi%3D1%7D%5E%7Bn%7D%7BE_i%7D+%2A+ln%28E_i+%2F+A_i%29+%5Ctag%7B4%7D" alt="psi = \sum_{i=1}^{n}{(A_i - E_i)} * ln(A_i / E_i) \\   psi = \sum_{i=1}^{n}{A_i} * ln(A_i / E_i)  + \sum_{i=1}^{n}{E_i} * ln(E_i / A_i) \tag{4}" eeimg="1"/> </p><p>将PSI计算公式变形后可以分解为2项，其中：</p><blockquote>第1项：实际分布（A）与预期分布（E）之间的KL散度—— <img src="https://www.zhihu.com/equation?tex=KL%28A+%7C%7C+E%29" alt="KL(A || E)" eeimg="1"/> <br/>第2项：预期分布（E）与实际分布（A）之间的KL散度—— <img src="https://www.zhihu.com/equation?tex=KL%28E+%7C%7C+A%29" alt="KL(E || A)" eeimg="1"/> </blockquote><p>因此，<b>PSI本质上是实际分布（A）与预期分布（E）的KL散度的一个对称化操作</b>。其<b>双向</b>计算相对熵，并把两部分相对熵相加，从而更为全面地描述两个分布的差异。</p><p>至此，已经回答了上文的问题Q1——<b>为什么要在计算公式中引入对数？</b></p><p>可能有人还会问，那为什么不把PSI定义成下式呢？</p><p><img src="https://www.zhihu.com/equation?tex=%5Cfrac%7B+KL%28A+%7C%7C+E%29+%2B+KL%28E+%7C%7C+A%29+%7D+%7B2%7D+%5Ctag%7B5%7D" alt="\frac{ KL(A || E) + KL(E || A) } {2} \tag{5}" eeimg="1"/> </p><p>按笔者理解，一是为了保证数学公式的优雅；二是只要在同一尺度上比较，同时放大2倍也无所谓。</p><p>再回到上文的问题Q2——<b>为什么log的底数是自然常数e，而不是其他常数（比如2）</b>？按笔者理解，两者都可以，从信息量角度而言，底数为2或许更为合理。</p><p>至于问题Q3——<b>为什么不定义为“SUM(实际占比 - 预期占比）”？</b>这是因为有正有负，会存在相互抵消的现象。另一方面，也确实没有从相对熵角度来定义更为合理。</p><figure data-size="normal"><noscript><img src="https://pic4.zhimg.com/v2-91d772eb1f18088b787b2a3be1c89f74_b.jpg" data-size="normal" data-rawwidth="1760" data-rawheight="572" class="origin_image zh-lightbox-thumb" width="1760" data-original="https://pic4.zhimg.com/v2-91d772eb1f18088b787b2a3be1c89f74_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;1760&#39; height=&#39;572&#39;&gt;&lt;/svg&gt;" data-size="normal" data-rawwidth="1760" data-rawheight="572" class="origin_image zh-lightbox-thumb lazy" width="1760" data-original="https://pic4.zhimg.com/v2-91d772eb1f18088b787b2a3be1c89f74_r.jpg" data-actualsrc="https://pic4.zhimg.com/v2-91d772eb1f18088b787b2a3be1c89f74_b.jpg"/><figcaption>图 6 - PSI与KL散度之间的关系</figcaption></figure><h2><b>Part 5. PSI指标的业务应用</b></h2><p>我们在业务上一般怎么用呢？一般以训练集（INS）的样本分布作为预期分布，进而跨时间窗按月/周来计算PSI，得到如图6所示的Monthly PSI Report，进而剔除不稳定的变量。同理，在模型上线部署后，也将通过PSI曲线报表来观察模型的稳定性。</p><blockquote>入模变量保证稳定性，变量监控<br/>模型分数保证稳定性，模型监控</blockquote><p>根据建模经验，给出一些建议：</p><p>1. 实际评估需要分不同粒度：时间粒度（按月、按样本集）、订单层次（放贷层、申请层）、人群（若没有分群建模，可忽略）。</p><p>2. 先在放贷样本上计算PSI，剔除不稳定的特征；再对申请样本抽样（可能数据太大），计算PSI再次筛选。之前犯的错误就是只在放贷样本上评估，后来在全量申请订单上评估时发现并不稳定，导致返工。</p><p>3. 时间窗尽可能至今为止，有可能建模时间窗稳定，但近期时间窗出现不稳定。</p><p>4. PSI只是一个宏观的指标，建议先看变量数据分布（EDD），看分位数跨时间变化来检验数据质量。我们无法得知PSI上升时，数据分布是左偏还是右偏。因此，<b>建议把PSI计算细节也予以保留，便于在模型不稳定时，第一时间排查问题。</b></p>

&nbsp;
## 代码
```
def psi_for_continue_var(expected_array, actual_array, bins=10, bucket_type='bins', detail=False, save_file_path=None):
    '''
    ----------------------------------------------------------------------
    功能: 计算连续型变量的群体性稳定性指标（population stability index ,PSI）
    ----------------------------------------------------------------------
    :param expected_array: numpy array of original values，基准组
    :param actual_array: numpy array of new values, same size as expected，比较组
    :param bins: number of percentile ranges to bucket the values into，分箱数, 默认为10
    :param bucket_type: string, 分箱模式，'bins'为等距均分，'quantiles'为按等频分箱
    :param detail: bool, 取值为True时输出psi计算的完整表格, 否则只输出最终的psi值
    :param save_file_path: string, csv文件保存路径. 默认值=None. 只有当detail=Ture时才生效.
    ----------------------------------------------------------------------
    :return psi_value: 
            当detail=False时, 类型float, 输出最终psi计算值;
            当detail=True时, 类型pd.DataFrame, 输出psi计算的完整表格。最终psi计算值 = list(psi_value['psi'])[-1]
    ----------------------------------------------------------------------
    示例：
    >>> psi_for_continue_var(expected_array=df['score'][:400],
                             actual_array=df['score'][401:], 
                             bins=5, bucket_type='bins', detail=0)
    >>> 0.0059132756739701245
    ------------
    >>> psi_for_continue_var(expected_array=df['score'][:400],
                             actual_array=df['score'][401:], 
                             bins=5, bucket_type='bins', detail=1)
    >>>
    	score_range	expecteds	expected(%)	actucalsactucal(%)ac - ex(%)ln(ac/ex)psi	max
    0	[0.021,0.2095]	120.0	30.00	152.0	31.02	1.02	0.033434	0.000341	
    1	(0.2095,0.398]	117.0	29.25	140.0	28.57	-0.68	-0.023522	0.000159	
    2	(0.398,0.5865]	81.0	20.25	94.0	19.18	-1.07	-0.054284	0.000577	<<<<<<<
    3	(0.5865,0.7751]	44.0	11.00	55.0	11.22	0.22	0.019801	0.000045	
    4	(0.7751,0.9636]	38.0	9.50	48.0	9.80	0.30	0.031087	0.000091	
    5	>>> summary	400.0	100.00	489.0	100.00	NaN	NaN	0.001214	<<< result
    ----------------------------------------------------------------------
    知识:
    公式： psi = sum(（实际占比-预期占比）* ln(实际占比/预期占比))
    一般认为psi小于0.1时候变量稳定性很高，0.1-0.25一般，大于0.25变量稳定性差，建议重做。
    相对于变量分布(EDD)而言, psi是一个宏观指标, 无法解释两个分布不一致的原因。但可以通过观察每个分箱的sub_psi来判断。
    ----------------------------------------------------------------------
    '''
    import math
    import numpy as np
    import pandas as pd
    
    expected_array = pd.Series(expected_array).dropna()
    actual_array = pd.Series(actual_array).dropna()
    if isinstance(list(expected_array)[0], str) or isinstance(list(actual_array)[0], str):
        raise Exception("输入数据expected_array只能是数值型, 不能为string类型")
        
    """step1: 确定分箱间隔"""
    def scale_range(input_array, scaled_min, scaled_max):
        '''
        ----------------------------------------------------------------------
        功能: 对input_array线性放缩至[scaled_min, scaled_max]
        ----------------------------------------------------------------------
        :param input_array: numpy array of original values, 需放缩的原始数列
        :param scaled_min: float, 放缩后的最小值
        :param scaled_min: float, 放缩后的最大值
        ----------------------------------------------------------------------
        :return input_array: numpy array of original values, 放缩后的数列
        ----------------------------------------------------------------------
        '''
        input_array += -np.min(input_array) # 此时最小值放缩到0
        if scaled_max == scaled_min:
            raise Exception('放缩后的数列scaled_min = scaled_min, 值为{}, 请检查expected_array数值！'.format(scaled_max))
        scaled_slope = np.max(input_array) * 1.0 / (scaled_max - scaled_min)
        input_array /= scaled_slope
        input_array += scaled_min
        return input_array
    
    # 异常处理，所有取值都相同时, 说明该变量是常量, 返回999999
    if np.min(expected_array) == np.max(expected_array):
        return 999999
    
    breakpoints = np.arange(0, bins + 1) / (bins) * 100 # 等距分箱百分比
    if 'bins' == bucket_type:        # 等距分箱
        breakpoints = scale_range(breakpoints, np.min(expected_array), np.max(expected_array))
    elif 'quantiles' == bucket_type: # 等频分箱
        breakpoints = np.stack([np.percentile(expected_array, b) for b in breakpoints])
 
    """step2: 统计区间内样本占比"""
    def generate_counts(arr, breakpoints):
        '''
        ----------------------------------------------------------------------
        功能: Generates counts for each bucket by using the bucket values 
        ----------------------------------------------------------------------
        :param arr: ndarray of actual values
        :param breakpoints: list of bucket values
        ----------------------------------------------------------------------
        :return cnt_array: counts for elements in each bucket, length of breakpoints array minus one
        :return score_range_array: 分箱区间
        ----------------------------------------------------------------------
        '''
        def count_in_range(arr, low, high, start):
            '''
            ----------------------------------------------------------------------
            功能: 统计给定区间内的样本数(Counts elements in array between low and high values)
            ----------------------------------------------------------------------
            :param arr: ndarray of actual values
            :param low: float, 左边界
            :param high: float, 右边界
            :param start: bool, 取值为Ture时，区间闭合方式[low, high],否则为(low, high]
            ----------------------------------------------------------------------
            :return cnt_in_range: int, 给定区间内的样本数
            ----------------------------------------------------------------------
            '''
            if start:
                cnt_in_range = len(np.where(np.logical_and(arr >= low, arr <= high))[0])
            else:
                cnt_in_range = len(np.where(np.logical_and(arr > low, arr <= high))[0])
            return cnt_in_range
 
        cnt_array = np.zeros(len(breakpoints) - 1)
        score_range_array = [''] * (len(breakpoints) - 1)
        for i in range(1, len(breakpoints)):
            cnt_array[i-1] = count_in_range(arr, breakpoints[i-1], breakpoints[i], i==1)
            if 1 == i:
                score_range_array[i-1] = '[' + str(round(breakpoints[i-1], 4)) + ',' + str(round(breakpoints[i], 4)) + ']'
            else:   
                score_range_array[i-1] = '(' + str(round(breakpoints[i-1], 4)) + ',' + str(round(breakpoints[i], 4)) + ']'
                                                                                
        return (cnt_array, score_range_array)
 
    expected_cnt = generate_counts(expected_array, breakpoints)[0]
    expected_percents = expected_cnt / len(expected_array)
    actual_cnt = generate_counts(actual_array, breakpoints)[0]
    actual_percents = actual_cnt / len(actual_array)
    delta_percents = actual_percents - expected_percents
    score_range_array = generate_counts(expected_array, breakpoints)[1]
                                                                               
    """step3: 区间放缩"""
    def sub_psi(e_perc, a_perc):
        '''
        ----------------------------------------------------------------------
        功能: 计算单个分箱内的psi值。Calculate the actual PSI value from comparing the values.
             Update the actual value to a very small number if equal to zero
        ----------------------------------------------------------------------
        :param e_perc: float, 期望占比
        :param a_perc: float, 实际占比
        ----------------------------------------------------------------------
        :return value: float, 单个分箱内的psi值
        ----------------------------------------------------------------------
        '''
        if a_perc == 0: # 实际占比
            a_perc = 0.001
        if e_perc == 0: # 期望占比
            e_perc = 0.001
        value = (e_perc - a_perc) * np.log(e_perc * 1.0 / a_perc)
        return value
    
    """step4: 得到最终稳定性指标"""
    sub_psi_array = [sub_psi(expected_percents[i], actual_percents[i]) for i in range(0, len(expected_percents))]
    if detail:
        psi_value = pd.DataFrame()
        psi_value['score_range'] = score_range_array
        psi_value['expecteds'] = expected_cnt
        psi_value['expected(%)'] = expected_percents * 100
        psi_value['actucals'] = actual_cnt
        psi_value['actucal(%)'] = actual_percents * 100
        psi_value['ac - ex(%)'] = delta_percents * 100
        psi_value['actucal(%)'] = psi_value['actucal(%)'].apply(lambda x: round(x, 2))
        psi_value['ac - ex(%)'] = psi_value['ac - ex(%)'].apply(lambda x: round(x, 2))
        psi_value['ln(ac/ex)'] = psi_value.apply(lambda row: np.log((row['actucal(%)'] + 0.001) \
                                                                  / (row['expected(%)'] + 0.001)), axis=1)
        psi_value['psi'] = sub_psi_array
        flag = lambda x: '<<<<<<<' if x == psi_value.psi.max() else ''
        psi_value['max'] = psi_value.psi.apply(flag)
        psi_value = psi_value.append([{'score_range':'>>> summary', 
                                       'expecteds': sum(expected_cnt),
                                       'expected(%)':100, 
                                       'actucals': sum(actual_cnt),
                                       'actucal(%)':100, 
                                       'ac - ex(%)': np.nan,
                                       'ln(ac/ex)': np.nan,
                                       'psi': np.sum(sub_psi_array),
                                       'max':'<<< result'}], ignore_index=True)
        if save_file_path:
            if not isinstance(save_file_path, str):
                raise Exception('参数save_file_path类型必须是str, 同时注意csv文件后缀!')
            elif not save_file_path.endswith('.csv'):
                raise Exception('参数save_file_path不是csv文件后缀，请检查!')
            psi_value.to_csv(save_file_path, encoding='utf-8', index=1)
    else:
        psi_value = np.sum(sub_psi_array)
 
    return psi_value
```
&nbsp;
## References
[风控模型—群体稳定性指标(PSI)深入理解应用](https://zhuanlan.zhihu.com/p/79682292)
