## 0、改进
Faiss是为稠密向量提供高效相似度搜索和聚类的框架，由Facebook AI Research研发。

Tensorflow 中的损失函数-loss专题汇总（知乎）

图谱本体构建工具：Protege

新闻分类的改进：利用cnn等分类；word 2vec维数50-300

&nbsp;
## 零、TransE
知识图谱通常是用一个三元组（前件h，关系r，后件t）来表示一条知识

TransE是Trans系列的第一篇文章，模型的基本想法是前件的向量表示h与关系的向量表示r之和与后件的向量表示t越接近越好，即h+r≈t  
这里的“接近”可以使用L1或L2范数进行衡量。
### 损失函数
要优化的目标函数使用带negative sampling的max margin损失函数，即L(y,y’)=max(0,margin−y+y’)，其中y表示正样本的得分，y’表示负样本的得分。  
最小化这个损失函数可以使正样本的得分越来越高而负样本的得分越来越低，但是两个得分差距大到一定程度（margin）就足够了，再大的话得到的loss也只是0。    
(margin算是一个超参数，需要针对不同模型看效果。我记得有几个模型就是margin=6的时候效果好一点。)

在这里因为正负样本得分用的是距离，所以要加负号，即最终的损失函数为
<figure data-size="normal"><noscript><img src="https://pic1.zhimg.com/v2-88b0bcd82e70d30e36fb66c29d79755e_b.jpg" data-caption="" data-size="normal" data-rawwidth="640" data-rawheight="50" class="origin_image zh-lightbox-thumb" width="640" data-original="https://pic1.zhimg.com/v2-88b0bcd82e70d30e36fb66c29d79755e_r.jpg"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;640&#39; height=&#39;50&#39;&gt;&lt;/svg&gt;" data-caption="" data-size="normal" data-rawwidth="640" data-rawheight="50" class="origin_image zh-lightbox-thumb lazy" width="640" data-original="https://pic1.zhimg.com/v2-88b0bcd82e70d30e36fb66c29d79755e_r.jpg" data-actualsrc="https://pic1.zhimg.com/v2-88b0bcd82e70d30e36fb66c29d79755e_b.jpg"/></figure><p class="ztext-empty-paragraph"><br/></p><p>其中</p><p class="ztext-empty-paragraph"><br/></p><figure data-size="normal"><noscript><img src="https://pic1.zhimg.com/v2-6de2b71d0a70360e6f44a1623278a59d_b.jpg" data-caption="" data-size="normal" data-rawwidth="261" data-rawheight="50" class="content_image" width="261"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;261&#39; height=&#39;50&#39;&gt;&lt;/svg&gt;" data-caption="" data-size="normal" data-rawwidth="261" data-rawheight="50" class="content_image lazy" width="261" data-actualsrc="https://pic1.zhimg.com/v2-6de2b71d0a70360e6f44a1623278a59d_b.jpg"/></figure>

论文中获得负样本并不是随机选取其他的三元组，其中还有一个trick，将正样本三元组中的前件或后件替换为一个随机的实体已获取负样本。

但是模型的简单也就带来了问题，它只适合处理一对一的关系，不适合一对多/多对一的关系。   
举个例子，有两个三元组（中国科学院大学，地点，北京）和（颐和园，地点，北京），使用TransE进行表示的话会得到中国科学院大学的表示向量和颐和园的表示向量很接近，甚至完全相同。但是它们的亲密度实际上可能没有这么大。

TransH模型的目的就在于处理一对多/多对一的关系问题，而又不过分增加模型的复杂度和训练难度。

TransE和TransH模型都假设实体和关系是在同一个语义空间的向量，这样相似的实体会在空间中相近的位置，然而每一个实体都可以有很多方面，而不同的关系关注的是实体不同的方面。因此，TransR模型对不同的关系建立各自的关系空间，在计算时先将实体映射到关系空间进行计算。因为是在关系空间做向量叠加，所以这个模型叫做TransR。
  
[Trans系列知识表示学习方法梳理](https://zhuanlan.zhihu.com/p/32993044)

&nbsp;
## 一、知识图谱
Neo4j 社区版最多支持 320 亿个节点、320 亿个关系和 640 亿个属性，而企业版没有限制。 

本体与实体的关系（is a）上下位 

&nbsp;
## 二、基于任务的问答
示例：大族激光 涨幅30%  
```
bert+lstm+crf：slot+filing 
bert+cnn：分类 

from kashgari.embeddings import BERTEmbedding 
from kashgari.tasks.classification import CNNModel 
from kashgari.tasks.seq_labeling import BLSTMCRFModel 
```
### Bert的模型作为BLSTMCRF、CNN等的输入。
问答冷启动（没有用户问句，用正则较难匹配，如实体识别和链接：李娜的问句，而Bert可以较好的解决问题)

槽位填充6M50几万行，意图识别2M约3万句语料。

BERTEmbedding（中文base400M模型（字级别的嵌入，维基百科），sequence_length=35或100、600（demo）)

bert用于分类（微调加一层softmax）

[Kashgari](https://github.com/BrikerMan/Kashgari)  
[Kashgari](https://kashgari-zh.bmio.net/)

&nbsp;
## 三、问答系统
问答系统可分为面向任务、面向知识和面向聊天三类，从关键技术上分，还可以把其分成基于搜索技术的问答系统、基于协同的问答系统、基于知识库的问答系统。
```
1.面向任务的问答系统是一种闭域应用，通常使用基于规则的或基于模板的方法，并采用对话状态跟踪技术。
在帮助服务中所使用的槽位填充方法，就是一种基于模板的方法。

2.面向知识的问答系统可用于闭域和开放域，通常使用以数据为驱动的信息检索模型。
该类方法基于从问答知识库中查找与提问问题最匹配的知识。一份最新的研究工作尝试使用基于神经网络的方法实现问题间的匹配。
最常用的一种方法是基于知识图谱与信息检索相结合的方法，检索知识图谱可给出高准确率的问答，并以信息检索为补充。

3.基于聊天的问答系统常用于开放域，使用方法包括信息检索和生成模型。
```

&nbsp;
## 四、tensorflow 
TensorFlow进行GPU和CPU切换(强制使用CPU)：   
在代码的开头，所有 import 之前，加入下面两行代码： 
```
import os 
os.environ["CUDA_VISIBLE_DEVICES"] = "-1" # 这一行注释掉就是使用cpu，不注释就是使用gpu 
--------------------- 

检测tensorflow是否使用gpu进行计算： 
import tensorflow as tf 
sess = tf.Session(config=tf.ConfigProto(log_device_placement=True)) 
查看日志信息若包含gpu信息，就是使用了gpu。 
其他方法：跑计算量大的代码，通过 nvidia-smi 命令查看gpu的内存使用量。 
--------------------- 
```
### TensorFlow之CPU、GPU设置: 
1.指定GPU 
```
法1:  
import os 
os.environ["CUDA_DEVICE_ORDER"] = "PCI_BUS_ID" 
os.environ["CUDA_VISIBLE_DEVICES"] = "1" 
上边表示使用第二块GPU运行程序，如果要使用多块，如第一块和第三块，可使用如下方法指定 
os.environ["CUDA_VISIBLE_DEVICES"] = "0,2" 
如果想屏蔽输出信息，使用以下设置 
os.environ['TF_CPP_MIN_LOG_LEVEL']='3' #close log output 

法2: 
import tensorflow as tf 
with tf.device('/gpu:1'): 
```
2.指定CPU 
```
cpu_num=10#指定使用的CPU个数 
config = tf.ConfigProto(device_count={"CPU": cpu_num}, 
inter_op_parallelism_threads = cpu_num, 
intra_op_parallelism_threads = cpu_num, 
log_device_placement=True) 
# 开始训练 
with tf.Session(config=config) as sess: 
#以下编写自己的代码 
log_device_placement = True可以把程序运行时跑的设备情况给输出 
```

&nbsp;
## 五、HMM和CRF
HMM和CRF可以根据上下文识别未登陆词，但这种模型缺乏领域自适应能力。   
概率有向图：贝叶斯网络   
概率无向图：马尔可夫网络   
两者差别在如何求联合概率分布   

**条件随机场是给定随机变量X条件下，随机变量Y的马尔可夫随机场**。

### CRF++
CRF把分词当做字的词位分类问题，采用4-tag( B(Begin，词首), E(End，词尾), M(Middle，词中),  S(Single,单字词))标记集；   
CRF分词的过程就是对词位标注后，将B和E之间的字，以及S单字构成分词。   
crf++的使用方法：  
```
1.安装
第一步：准备训练语料
“ 人们 常 说 生活 是 一 部 教科书 
脚本 make_crf_train.py，将训练语料转换为CRF++训练用的语料格式(2列，4-tag)：
“ S
人 B
们 E
常 S
说 S
生 B
活 E
是 S
一 S
部 S
教 B
科 M
书 E

第二步：训练模型
准备好训练语料，就可以利用crf的训练工具crf_learn来训练模型了

第三步：准备测试语料并进行测试
有了模型，现在我们需要做得还是准备一份CRF++用的测试语料，然后利用CRF++的测试工具crf_test进 行字标注。

第四步：将标注的词位信息转化为分词结果
```

&nbsp;
## 六、其他
### 1.gensim训练word2vec可以用两种模型？ 
对，参数sg =1是skip-gram算法，默认sg =0为cbow 算法。

### 2.tf实现bilstm+crf，crf是怎么调用的？ 
crf++的使用

### 3.Trie树
又称单词查找树/ 字典树/前缀树或键树，是一种树形结构，是一种哈希树的变种   
Trie的核心思想是空间换时间。利用字符串的公共前缀来降低查询时间的开销以达到提高效率的目的。   
Trie树的基本性质可以归纳为： 
```
（1）根节点不包含字符，除根节点以外每个节点只包含一个字符。
（2）从根节点到某一个节点，路径上经过的字符连接起来，为该节点对应的字符串。 
（3）每个节点的所有子节点包含的字符串不相同。
```
### 4.tensorflow中 bilstm+crf实现代码：
训练函数tf.contrib.crf.crf_log_likehood和解码函数tf.contrib.crf.viterbi.decode

### 5.Limes（知识图谱的实体链接发现框架）
ExactMatch是严格的相似度匹配比较算法，review数量为0，准确度precision很高，但召回率recall很 低；Consine则比较均衡，准确度和exactmatch相似，review数目很大。
