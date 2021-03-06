## psi
金融大数据评分系统监控模块需要根据评分结果判断当前评分服务是否正常，据对现有评分数据的分析，
监控系统采用基于成熟的PSI指数的监控机制，通过把各种系统主要异常代码分别设立监控类别，结合目标评分的评分的分布
，实现对如重大异常情况的监控，其中有一个指标PSI，统计学原理如下：

### 1.特征稳定性
**所谓特征稳定性，就是关注该特征的取值随着时间的推移会不会发生大的波动。**   

通常采用PSI（PopulationStability Index，群体稳定性指数）指标评估特征稳定性。计算公式如下：

PSI是对两个日期的特征数据进行计算，可以任选其一作为base集，另一则是test集（也有其他叫法为expected集和actual集）。

下面介绍特征的PSI是如何计算出来的，有了这个，就可以读懂上面的公式了：
```
• 特征取值等频分段：对这个特征在base集的取值进行等频划分（通常等频分10份即可），用字母i表示第i个分段区间。

• 计算：p
统计落在每个分段区间内的目标数量（如果是用户特征就是用户数，如果是门店特征就是门店数，etc），进一步得到数量占比，
表示该特征在base集中第i个取值分段中的数量占比。

• 计算：
继续按照第2步计算得到
，注意，分段还是采用第1步中产出的分段（依据base集产出的分段）。

• 根据前文的公式即可计算得到该特征基于这两个日期的PSI。
```
### 2.模型稳定性
相比特征稳定性，模型稳定性涉及的东西比较多，需要根据模型的具体应用方式选择性进行关注。通常，模型PSI是必须关注的一个指标。

#### 模型PSI
有了前文对特征PSI的介绍，理解模型PSI就非常简单了。

二分类模型的输出一般都会有一个取值为0~1之间的概率值（记作：prediction_prob），模型PSI监控的就是这个值的稳定性。

将模型产出的prediction_prob理解为一个特征，就可以像计算特征PSI一样计算得到模型PSI了，不同的地方在于，特征PSI一般是对很多特征一起做计算（假如准备了200个特征进行建模，那就是对200个特征计算PSI），而模型PSI通常只是对prediction_prob这一个字段做计算。计算方式同前文所述完全一样，PAI组件的使用也没有任何不同，不再赘述。
### 3.模型稳定性的其他实践
#### 3.1 消除波动性
对于二分类模型，在实际业务中通常会直接拿着prediction_prob去用。例如，对于某个风险识别场景，根据prediction_prob对用户进行准入或拦截（假如设定阈值为0.6，则prediction_prob小于0.6的用户被拦截，不小于0.6的用户被准入）。

但是会存在一些应用场景对稳定性要求更高。为了消除double型可能带来的波动性，可以将小数映射为整数再使用，我们将这个过程称为Rank。

具体要将0-1的小数值映射到1-10还是1-100亦或是1-1000的整数区间，完全取决于应用场景对这个数值的精细化程度。这样做映射以消除波动性是有道理的，它相当于把一定范围内的波动屏蔽了。例如，某信用风险模型在10月份对用户小C的打分为0.61，在11月份的打分为0.69（假如打分的差异仅仅因为该用户在双11期间疯狂买买买所致，而事实上短暂性的买买买并不应该对用户的信用风险评估造成影响），如果映射为1-10的整数区间后，连续两个月份的打分都是7（[0.6, 0.69]整个区间均被映射为7），从而达到屏蔽波动性的目的。

将0-1小数映射到整数区间的做法非常简单：首先对原始小数列求分位数（如果要映射为1-10的得分区间就求十个分位点，如果要映射为1-100的得分区间就求一百个分位点），然后根据各分位点处的取值将原始值分为确定数量的区间（如果要映射为1-10的得分区间就是10个区间，如果要映射为1-100的得分区间就是100个区间），每个区间映射为一个整数值，映射完毕。
#### 3.2 Rank迁移
当把prediction_prob Rank到整数区间后，就有必要对Rank后的结果实施必要的监控了。有两个事情值得去做，其一是对分位点进行按月迁移监控（看分位点有没有随着时间的推移产生波动），其二是对Rank后的整数进行月份间波动监控（看看每连续两个月之间，全量用户得分的波动性）。


&nbsp;
## reference
[金融大数据监控PSI（特征稳定性）指标详解](https://blog.csdn.net/wshl1234567/article/details/89961873)      
[风控模型—特征稳定性指标(CSI)深入理解应用](https://zhuanlan.zhihu.com/p/86559671)
