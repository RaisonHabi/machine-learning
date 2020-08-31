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
