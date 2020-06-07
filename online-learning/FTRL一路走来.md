<h1><span style="font-size:24px;">二、 逻辑回归</span></h1>
<span style="font-size:18px;">在实际广告点击率预估的应用中，样本数目和特征(逻辑回归粗暴离散化后)的数目均可以达到上亿纬度，而LR因为其简单和易并行，并且基于复杂的特征工程后也能得到非常好的效果，所以在工业界获得了广泛的应用。</span></p>
<p><span style="font-size:18px;">      逻辑回归，相对于线性回归是用来处理目标函数是离散数值的情况。它的映射函数和损失函数分别为：</span></p>
<p><span style="font-size:18px;">           <img src="https://img-blog.csdn.net/20160502175425792?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="200" height="50" alt="" />           
                                            （1）                                                                                    <img src="https://img-blog.csdn.net/20160502175410463?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="600" height="45" alt="" /> 
           （2）</span></p>
<p><span style="font-size:18px;">      使用梯度下降法进行求解，得到迭代公式：</span></p>
<p><span style="font-size:18px;">           <img src="https://img-blog.csdn.net/20160502180239493?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="300" height="50" alt="" /></span></p>
<p><span style="font-size:18px;">1.  逻辑回归的优缺点：</span></p>
<p><span style="font-size:18px;">      优点：  </span></p>
<p><span style="font-size:18px;">          a. 简单</span></p>
<p><span style="font-size:18px;">          b. 易于并行、速度快</span></p>
<p><span style="font-size:18px;">      缺点：</span></p>
<p><span style="font-size:18px;">          a. 需要复杂的特征工程</span></p>
<p><span style="font-size:18px;">      注意事项：</span></p>
<p><span style="font-size:18px;">          a. 输入特征需要离散化。</span></p>
<p><span style="font-size:18px;"></span></p>
<h1><span style="font-size:24px;">三、 SGD算法</span></h1>
<p></p>
<p>         <span style="font-size:18px;">对于如上LR的迭代公式来说，我们可以得到GD（Gradient Descent）的求解算法。(W为求解的参数，l(w, z)为损失函数）。</span></p>
<p><span style="font-size:18px;">      <img src="https://img-blog.csdn.net/20160502205556989?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="600" height="150" alt="" /></span></p>
<p><span style="font-size:18px;">      可是如果我们对参数做一轮迭代求解，都需要遍历所有的样本，这在实际应用中未免迭代速度太慢，模型更新速度也太慢。而且做机器学习的时候往往更多的数据意味着更好的效果，我们能把线上实时的数据和样本标注也利用进来么？答案是 Yes。</span><span style="font-size:18px;">仔细研究参数的迭代我们可以发现，都是给定一个初始的参数W，通过迭代逐步求解出当前W下降的方向并更新W直到损失函数稳定在最小点。那么我们是不是可以取部分训练集作为原训练集的子集，使用这些子集做迭代，并逐步求解W的下降方向，逐步对W进行更新（理论证明未知）。特别的，如果我们每次取原训练样本的一个训练样本，对W的值逐步进行更新，那么我们就得到了SGD算法。</span></p>
<p>             <img src="https://img-blog.csdn.net/20160502205752367?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="600" height="200" alt="" /><br /></p>
<p>         <span style="font-size:18px;">   与SGD比较，GD需要每次扫描所有的样本以计算一个全局梯度，SGD则每次只针对一个观测到的样本进行更新。通常情况下SGD可以更快的逼近最优值，而且SGD每次更新只需要一个样本，使得它很适合进行增量或者在线计算（也就是所谓的Online learning）。</span></p>
<p><span style="font-size:18px;">         特别的，迭代和选取模型的时候我们经常希望得到更加稀疏的模型，这不仅仅起到了特征选择的作用，也降低了预测计算的复杂度。<span style="font-size:18px;">在实际使用LR的时候我们会使用L1或者L2正则，避免模型过拟合和增加模型的鲁棒性。在GD算法下，L1正则化通常能得到更加稀疏的解；可是在SGD算法下模型迭代并不是沿着全局梯度下降，而是沿着某个样本的梯度进行下降，这样即使是L1正则也不一定能得到稀疏解。在后面的优化算法中，稀疏性是一个主要追求的目标。</span></span></p>
<p></p>
<h1><span style="font-size:24px;">四、 TG算法</span></h1>
<h2><span style="font-size:18px;">1.  L1 正则化法</span></h2>
<p><span style="font-size:18px;">        由于L1正则项在0处不可导，往往会造成平滑的凸优化问题变成非平滑的凸优化问题，因此可以采用次梯度(Subgradient)来计算L1正则项的梯度。权重更新方式为：</span></p>
<p><span style="font-size:18px;">         <img src="https://img-blog.csdn.net/20160502235520026?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /><br /></span></p>
<p><span style="font-size:18px;">       其中<img src="https://img-blog.csdn.net/20160502235938218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />是一个标量，为L1正则化的参数；v是一个向量，<span style="font-size:18px;">sgn(v)为符号函数；<img src="https://img-blog.csdn.net/20160503000417904?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />称为学习率，</span>通常将其设置为<img src="https://img-blog.csdn.net/20160503000235623?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />的函数；<img src="https://img-blog.csdn.net/20160503000519787?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />代表第t次迭代中损失函数的梯度。</span></p>
<h2><span style="font-size:18px;">2.  简单截断法</span></h2>
<p><span style="font-size:18px;"></span></p>
<p><span style="font-size:18px;">         既然L1正则化在Online模式下也不能产生更好的稀疏性，而稀疏性对于高维特征向量以及大数据集又特别的重要，我们应该如何处理的呢？</span></p>
<p><span style="font-size:18px;">         简单粗暴的方法是设置一个阀值，当W的某纬度的系数小于这个阀值的时候，将其直接设置为0。这样我们就得到了简单截断法。简单截断法以k为窗口，当t/k不为整数时采用标准的SGD进行迭代，当t/k为整数时，权重更新方式如下：</span></p>
<p><span style="font-size:18px;">           <img src="https://img-blog.csdn.net/20160503001029860?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /></span></p>
<p><span style="font-size:18px;">           <img src="https://img-blog.csdn.net/20160503000957091?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /><br /></span></p>
<p><span style="font-size:18px;">         这里<img src="https://img-blog.csdn.net/20160503001146704?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />是一个正数；V是一个向量。</span></p>
<h2><span style="font-size:18px;">3.  梯度截断法</span></h2>
<p><span style="font-size:18px;"><span style="font-size:18px;">         简单截断法法简单且易于理解，但是在实际训练过程中的某一步，W的某个特征系数可能因为该特征训练不足引起的，简单的截断过于简单粗暴(too aggresive)，会造成该特征的缺失。那么我们有没有其他的方法，使得权重的归零和截断处理稍微温柔一些呢？对，这就是梯度截断法，简单截断法和梯度截断法对特征权重的处理映射图对比如下：</span><br /></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">            <img src="https://img-blog.csdn.net/20160503001708628?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /><br /></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">           梯度截断法的迭代公式如下：</span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">           <img src="https://img-blog.csdn.net/20160503001832004?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="font-size:18px;" /><span style="font-size:18px;"> </span><br /></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;">           <img src="https://img-blog.csdn.net/20160503001933567?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /> 
                           (3)</span></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;">           同样的梯度截断法也是以k为窗口，每k步进行一次截断。当t/k不为整数时<img src="https://img-blog.csdn.net/20160503002200599?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />，当t/k为整数时<img src="https://img-blog.csdn.net/20160503002228583?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />。从公式(3)可以看出<img src="https://img-blog.csdn.net/20160503002355344?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />和<img src="https://img-blog.csdn.net/20160503002408656?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />决定了截断的区域，也决定了W的稀疏程度。这两个数值越大，截断区域越大，稀疏性也越强。尤其这两个值相等的时候，只需要调节一个参数就能控制稀疏性。根据公式(3)，得到TG的算法逻辑如下：</span></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;">         <img src="https://img-blog.csdn.net/20160503002652882?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="550" height="330" alt="" /></span></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">         特别的对(3)进行改写，得到描述特征权重的更新方式为：</span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">                <img src="https://img-blog.csdn.net/20160503003223355?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /><br /></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">                <img src="https://img-blog.csdn.net/20160503003300229?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">         如果令<img src="https://img-blog.csdn.net/20160504000727607?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /><img src="" alt="" />，截断公式变成：</span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">                <img src="https://img-blog.csdn.net/20160504000754785?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /><img src="" alt="" /></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">         此时TG退化成简单截断法。同样如果令<img src="https://img-blog.csdn.net/20160503003501636?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />，我们就可以得到L1正则化方法。</span></span></p>
<p></p>
<h1><span style="font-size:24px;">四、 FOBOS算法</span></h1>
<p><span style="font-size:18px;">          FOBOS(Forward-Backward Splitting)是由John Duchi和Yoram Singer提出的</span><span style="font-size:18px;">。FOBOS算法把正则化的梯度下降问题分成一个经验损失梯度下降迭代和一个最优化问题。其中第二个最优化问题有两项：第一项2范数那项表示不能离loss损失迭代结果太远，第二项是正则化项，用来限定模型复杂度、抑制过拟合和做稀疏化等。</span></p>
<p><span style="font-size:18px;">         <img src="https://img-blog.csdn.net/20160503215630088?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="450" height="95" alt="" /></span></p>
<p><span style="font-size:18px;">          由于求和公式中的每一项都是大于等于0的，所以公式(2)可以拆解成对特征权重每一纬度单独求解：</span></p>
<p><span style="font-size:18px;">          <img src="https://img-blog.csdn.net/20160503220315943?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />            (2)</span></p>
<p><span style="font-size:18px;">           首先假设w是(2)的最优解，则有<img src="https://img-blog.csdn.net/20160503230721605?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />。反正时代入，可以得到w=0是(2)的最优解。。。</span></p>
<p><span style="font-size:18px;">           对v和w的取值分别进行求解可以得到：</span></p>
<p><span style="font-size:18px;">           <img src="https://img-blog.csdn.net/20160504000345783?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /></span></p>
<p><span style="font-size:18px;">           <img src="https://img-blog.csdn.net/20160504000252111?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /></span></p>
<p><span style="font-size:18px;">           其中g_i^(t)为梯度G^(t)在纬度i上的取值。然后我们可以得到L1-FOBOS的算法逻辑：</span></p>
<p><span style="font-size:18px;">           <img src="https://img-blog.csdn.net/20160504001520460?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="500" height="250" alt="" /></span></p>
<p><span style="font-size:18px;">1.  L1-FOBOS与TG的关系  </span></p>
<p><span style="font-size:18px;">           从公式3)可以看出，L1-FOBOS 在每次更新W的时，对W的每个纬度都会加以判定，当满足<img src="https://img-blog.csdn.net/20160504001739101?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="150" height="30" alt="" />时对该纬度的特征进行截断，这个判定的含义是当一条样本的梯度不足以令对应纬度上的权重值发生足够大的变化时，认为在本次更新中该纬度不够重要，应当令其权重为0。</span></p>
<p><span style="font-size:18px;">           对于L1-FOBOS特征权重的各个纬度更新公式(3)，也可以写为如下形式：</span></p>
<p><span style="font-size:18px;">            <img src="https://img-blog.csdn.net/20160504002301189?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /> </span></p>
<p><span style="font-size:18px;">            如果令<img src="https://img-blog.csdn.net/20160504002337909?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />，L1-FOBOS与TG完全一致，L1-FOBOS是TG在特定条件下的特殊形式。</span></p>
<p></p>
<h1><span style="font-weight:normal;"><span style="font-size:24px;">五、 RDA算法</span></span></h1>
<p><span style="font-size:18px;">           之前的算法都是在SGD的基础上，属于梯度下降类型的方法，这类型的方法的优点是精度比较高，并且TG、FOBOS也能在稀疏性上得到提升。但是RDA却从另一个方面进行在线求解，并且有效提升了特征权重的稀疏性。RDA是Simple Dual Averaging Scheme的一个扩展，由Lin Xiao发表与2010年。</span></p>
<p><span style="font-size:18px;">          在RDA中，特征权重的更新策略包含一个梯度对W的积分平均值，正则项和一个辅助的严格凸函数。具体为：</span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20160523004450012?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="350" height="60" alt="" />。其中&lt;G(t),W&gt;表示梯度G对W的积分平均值，包含了之前所有梯度的平均值；<img src="https://img-blog.csdn.net/20160523004630576?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />为正则化项，<img src="https://img-blog.csdn.net/20160523004652319?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />表示一个非负且非自减序列，<img src="https://img-blog.csdn.net/20160523004822218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />是一个严格的凸函数。<br /></span></p>
<p></p>
<h2></h2>
<h2><span style="font-size:18px;">1.  算法原理</span></h2>
<p><span style="font-size:18px;"><span style="font-size:18px;">           </span>之前的算法都是在SGD的基础上，属于梯度下降类型的方法，这类型的方法的优点是精度比较高，并且TG、FOBOS也能在稀疏性上得到提升。但是RDA却从另一个方面进行在线求解，并且有效提升了特征权重的稀疏性。RDA是Simple Dual Averaging Scheme的一个扩展，由Lin Xiao发表于2010年。</span></p>
<p><span style="font-size:18px;">          L1-RDA特征权重各个纬度更新方式为：</span></p>
<p><span style="font-size:18px;">                <img src="https://img-blog.csdn.net/20160605174903972?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /></span></p>
<p><span style="font-size:18px;">          这里当某个纬度上累积梯度平均值小于阀值的时候，该纬度权重将被设置为0，特征稀疏性由此产生。</span></p>
<p><span style="font-size:18px;">          对比L1-FOBOS我们可以发现，L1-FOBOS是TG的一种特殊形式，在L1-FOBOS中，进行截断的判定条件是</span></p>
<p><img src="https://img-blog.csdn.net/20160605175111069?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /><span style="font-size:18px;">，通常会定义为正相关函数<img src="https://img-blog.csdn.net/20160605175153961?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />。因此L1-FOBOS的截断阀值为<img src="https://img-blog.csdn.net/20160605175214571?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />，随着t增加，这个阀值会逐渐降低。而相比较而言L1-RDA的截断阀值为，是一个固定的常数，因此可以认定L1-RDA比L1-FOBOS更加aggressive。此外L1-FOBOS判定是针对单次梯度计算进行判定，避免由于训练不足导致的截断问题。并且通过调节一个参数，很容易在精度和稀疏性上进行权衡。</span></p>
<h1>六、FTRL算法</h1>
<p></p>
<p style="font-family:Menlo;">
<span style="font-family:Menlo;"><span style="font-size:18px;">   有实验证明，L1-FOBOS这一类基于梯度下降的方法有较高的精度，但是L1-RDA却能在损失一定精度的情况下产生更好的稀疏性。如何能把这两者的优点同时体现出来的呢？这就是</span></span><br /></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;">1.  L1-FOBOS与L1-RDA在形式上的统一：</span></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;"><span></span>L1-FOBOS在形式上，每次迭代都可以表示为（这里我们令）</span></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;">FTRL综合考虑了FOBOS和RDA对于正则项和W的限制，其特征权重为：</span></p>
<p style="font-family:Menlo;">
<img src="https://img-blog.csdn.net/20160724151551846?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /><br /></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;">注意，公式(3)中出现了L2正则项<img src="https://img-blog.csdn.net/20160724151729521?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />，而论文[2]的公式中并没有这一项，但是在2013年发表的FTRL工程化实现的论文中却使用了L2正则化项。事实上该项的引入并不影响FTRL的稀疏性，仅仅相当于对最优化过程多了一个约束，使得结果求解更加平滑。</span></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;"><span></span>公司(3)看上去很复杂，更新特征权重貌似非常困难。不妨将其改写。将其最后一项展开等价于求解下面的这样一个最优化问题：<img src="https://img-blog.csdn.net/20160724151815303?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /></span></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;"><br /></span></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;">，于是针对特征权重的各个纬度将其拆解成N个独立的标量最小化问题。</span><span style="font-size:18px;">上式最后一项相对于W说是一个常数项，并且令<img src="https://img-blog.csdn.net/20160724151854929?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" />，上式等价于：<img src="https://img-blog.csdn.net/20160724151932039?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /></span></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;">到这里，我们遇到了与RDA求解类似的最优化问题。用相同的分析方法可以得到：</span></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;"><img src="https://img-blog.csdn.net/20160724152018197?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" /><br /></span></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;">上式可以看出，引入L2正则化对于FTRL结果的稀疏性产生任何影响。</span></p>
<p></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;">在一个标准OGD中使用的是一个全局学习策略，这个策略保证了学习率是一个正的非增长序列，对于每个特征的纬度都是一样的。</span></p>
<p style="font-family:Menlo;">
<span style="font-size:18px;">考虑特征纬度的变化率：如果特征1比特征2的变化更快，那么纬度1上学习率应该下降的比较快。我们就很容易可以用某个纬度上梯度分量来反映这种变化率。在FTRL中纬度i上的学习率是这样计算的：</span></p>
<span style="font-size:18px;"><span style="font-family:'Microsoft YaHei';">。由于，所以公式(4)中有，</span><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';line-height:27.136px;">这里的</span><img alt="" src="http://latex.codecogs.com/gif.latex?%5Calpha" style="display:inline-block;font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';line-height:27.136px;" /><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';line-height:27.136px;">和</span><img alt="" src="http://latex.codecogs.com/gif.latex?%5Cbeta" style="display:inline-block;font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';line-height:27.136px;" /><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';line-height:27.136px;">是需要输入的参数，公式(4)中学习率写成累加的形式，是为了方便理解后面FTRL的迭代计算逻辑。</span></span><br /><p><span style="font-size:18px;"><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';line-height:27.136px;"><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';font-size:17px;">伪码采用的事L1和L2混合正则，即实际的迭代是如下形式：</span><br /></span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';line-height:27.136px;"><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';font-size:17px;"><img src="https://img-blog.csdn.net/20160724152846473?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="500" height="400" /><br /></span></span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';line-height:27.136px;"><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';font-size:17px;">总结：</span></span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';line-height:27.136px;"><span style="font-family:'Helvetica Neue', Helvetica, STHeiTi, 'Microsoft YaHei';font-size:17px;"><span style="color:rgb(102,102,102);font-family:Lato, 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:18px;line-height:32.4px;"><span></span>从类型上来看，简单截断法、TG、FOBOS属于同一类，都是梯度下降类的算法,并且TG在特定条件可以转换成简单截断法和FOBOS；RDA属于简单对偶平均的扩展应用；FTRL可以视作RDA和FOBOS的结合，同时具备二者的优点。目前来看，RDA和FTRL是最好的稀疏模型Online
 Training的算法。FTRL并行化处理，一方面可以参考ParallelSGD，另一方面可以使用高维向量点乘，及梯度分量并行计算的思路。</span>

&nbsp;
## reference
[FTRL一路走来，从LR -> SGD -> TG -> FOBOS -> RDA -> FTRL](https://blog.csdn.net/china1000/article/details/51176654)
