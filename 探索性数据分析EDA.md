## 探索性数据分析
### EDA
探索性数据分析（Exploratory Data Analysis，简称EDA），摘抄网上的一个中文解释，  
是指对已有的数据（特别是调查或观察得来的原始数据）在尽量少的先验假定下进行探索，通过作图、制表、方程拟合、计算特征量等手段探索数据的结构和规律的一种数据分析方法。  
特别是当我们对面对大数据时代到来的时候，各种杂乱的“脏数据”，往往不知所措，不知道从哪里开始了解目前拿到手上的数据时候，探索性数据分析就非常有效。  
探索性数据分析是上世纪六十年代提出，其方法有美国统计学家John Tukey提出的。

在统计学中，探索性数据分析（EDA）是一种分析数据集以概括其主要特征的方法，通常使用可视化方法。  
可以使用或使用统计模型，但主要是EDA是为了了解数据在形式化建模或假设测试任务之外能告诉我们什么。  
探索性数据分析是John Tukey提拔的鼓励统计学家的研究数据，并尽可能提出假设，尽可能生成新的数据收集和实验。

EDA不同于初始数据分析（initial data analysis，IDA），它更集中于检查模型拟合和假设检验所需的假设，以及处理缺少的值，并根据需要进行变量转换。EDA包含IDA。

### 探索性分析的计划
```
1、Form hypotheses/develop investigation theme to explore形成假设，确定主题去探索

2、Wrangle data清理数据，网上有一个网址公布斯坦福有一个软件叫datawrangler可以供大家自己免费下载，用于探索数据分析，很快的解决数据清洗的工作，作为一个将来想成为数据科学家的人，处理“脏数据”，是我们必须走的路。这个软件我还没有试，我把链接发在下面，供爱学习的小伙伴好好学习。http://vis.stanford.edu/wrangler/

https://www.trifacta.com/products/wrangler/

https://www.douban.com/note/501799325/

3、Assess quality of data评价数据质量

4、Profile data数据报表

5、Explore each individual variable in the dataset探索分析每个变量

6、Assess the relationship between each variable and the target探索每个自变量与因变量之间的关系

7、Assess interactions between variables探索每个自变量之间的相关性

8、Explore data across many dimensions从不同的维度来分析数据
```

### 通过以上的探索性分析，你还可以做以下的工作：
```
1、写出一系列你自己做的假设，然后接着做更深入的数据分析

2、记录下自己探索过程中更进一步的数据分析过程

3、把自己的中间的结果给自己的同行看看，让他们能够给你一些更有拓展性的反馈、或者意见。不要独自一个人做，国外的思维就是知道了什么就喜欢open to everybody，要走出去，多多交流，打开新的世界。

4、将可视化与结果结合一起。探索性数据分析，就是依赖你好的模型意识，（在《深入浅出数据分析》P34中，把模型的敏感度叫心智模型，最初的心智模型可能错了，一旦自己的结果违背自己的假设，就要立即回去详细的思考）。所以我们在数据探索的尽可能把自己的可视化图和结果放一起，这样便于进一步分析。
```
## 实战
### 数据转换：
```
1、取对数log：当数据的峰值很高，通过将数据取对数能够将数据归一化处理。

2、连续变量分组（bin）：分组连续变量，能够更加简便的了解观测值的分布。

3、简化类别：一个单一的数据，往往类别太多会让人迷乱，一般不想超过8-10列，那就尽量找到重要的类别。（机器学习里面这一个部分很重要，和特征选择一样）
```
### 项目开始：
```
第一步，不可或缺的导入五大包：NumPy、Pandas、Matplotlib、Seaborn，Warnings。
特别主要的就是Seaborn，这个库目前可视化的能力已经超过之前对Python的理解，有很多人说R可视化效果很好，但是我觉得这个可视化一点都不差。
%matplotlib inline很多人不懂为什么会加上这个意思，这就是在jupter画图的时候，能够将可视化的图能够在结果中展现出来。
导入弹出红色烦人的warinngs包，让它们被忽略。
```
### 第二步，导入数据
### 第三步，探索变量
### 第四步，简单的描述统计（后面对数据描述做详细的处理）
### 第五步，数据切分
面板数据一些基本定义
```
横截面：一个时期内所有国家的数据

时间序列：一个国家随着时间推移的数据

面板数据：所有国家随着时间的推移数据

地理空间：所有地理上相互关联的数据
```
透视表pivot_table等的使用
### 第六步，数据质量评估
评估缺失值数据在所有数据字段中的普遍性，评估其丢失是随机还是系统的，并在缺少数据是确定模式；

标签包含给定字段丢失数据的默认值；

确定质量评估抽样策略和初始EDA；

时间数据类型，保证格式的一致性和粒度的数据，并执行对数据的所有日期的检查；

在多个字段捕捉相同或者相似的信息的情况下，了解它们之间的关系并评估最有效的字段使用；

查看每个字段数据类型：  
对于离散值类型，确保数据格式一致，评估不同值和唯一百分比的数据，并对答案的类型进行正确检查  
连续数据类型，进行描述性统计，并对值进行检查

```
导入三大包：missingno、pivottablejs、pandas_profiling

Missingno https://github.com/ResidentMario/missingno非常重要的一个包，
可视化的展示数据缺失的分布情况，能够查看缺失值的分布情况，缺失数据有哪些原因，哪些地方有丢失？里面的柱状图，热力图都可视化效果很好。

Pivottalblejs https://github.com/nicolaskruchten/jupyter_pivottablejs能够形成一个透视表，
将每个变量形成统计。https://github.com/nicolaskruchten/pivottable

Pandas_profiling https://github.com/JosPolfliet/pandas-profiling/blob/master/examples/meteorites.ipynb
```
### 第七步，评估每个变量与目标变量之间的相关性。
量分为两种，一种是离散变量，一个是连续变量。通过可视化的展示这些自变量的与因变量之间的相关性。

1、离散变量与离散变量

热力图（http://seaborn.pydata.org/generated/seaborn.heatmap.html?highlight=heatmap#seaborn.heatmap），

柱状图（http://seaborn.pydata.org/tutorial/categorical.html?highlight=bar%20plot#bar-plots）

2、离散变量与连续变量

箱体图http://seaborn.pydata.org/generated/seaborn.boxplot.html#seaborn.boxplot

小提琴图，http://seaborn.pydata.org/examples/simple_violinplots.html

直方图，http://seaborn.pydata.org/tutorial/distributions.html#histograms

3、连续变量与连续变量

散点图http://seaborn.pydata.org/examples/marginal_ticks.html?highlight=scatter

Hexbin图http://seaborn.pydata.org/tutorial/distributions.html#hexbin-plots

像高斯核图一样的图http://seaborn.pydata.org/tutorial/distributions.html#kernel-density-estimation

热力图http://seaborn.pydata.org/generated/seaborn.heatmap.html#seaborn.heatmap

联合图http://seaborn.pydata.org/generated/seaborn.jointplot.html#seaborn.jointplot

&nbsp;
## reference
[一文带你探索性数据分析(EDA)](https://www.jianshu.com/p/9325c9f88ee6)
