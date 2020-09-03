### 0.rnn与attention: 
完全的不依赖于RNN结构仅利用Attention机制的Transformer由于其
并行性和对全局信息的有效处理使其获得了较之前方法更好的翻译结果. 


#### bert: 创新：
mlm、nsp   
transformer encoder、无监督学习预训练、微调 

&nbsp;
### 1.多头attention（Multi-head attention）
结构如下图，Query，Key，Value首先经过一个线性变换，然后输入到放缩点积attention，**注意这里要做h次，其实也就是所谓的多头，每一次算一个头**。 

**而且每次Q，K，V进行线性变换的参数W是不一样的**。然后将h次的放缩点积attention结果进行拼接，再进行一次线性变换得到的值作为多头attention的结果。 

这样的**好处是可以允许模型在不同的表示子空间里学习到相关的信息**。 

&nbsp;
### 2.Self-attention即K=V=Q
例如输入一个句子，那么里面的**每个词都要和该句子中的所有词进行attention计算。目的是学习句子内部的词依赖关系，捕获句子的内部结构**。 

#### 对于使用自注意力机制的原因，从三个方面考虑（每一层的复杂度，是否可以并行，长距离依赖学习），并给出了和RNN，CNN计算复杂度的比较。 
如果输入序列n小于表示维度d的话，每一层的时间复杂度self-attention是比较有优势的。  
当n比较大时，作者也给出了一种解决方案self-attention（restricted）即每个词不是和所有词计算attention，而是只与限制的r个词去计算attention。 

在并行方面，多头attention和CNN一样不依赖于前一时刻的计算，可以很好的并行，优于RNN。 

在长距离依赖上，由于self-attention是每个词和所有词都要计算attention，所以不管他们中间有多长距离，最大的路径长度也都只是1。可以捕获长距离依赖关系。 

假如我们要翻译一个词组Thinking Machines，其中Thinking的输入的embedding vector用x1表示，Machines的embedding vector用 x2表示。    
当我们处理Thinking这个词时，我们需要计算句子中所有词与它的Attention Score，这就像将当前词作为搜索的query，去和句子中所有词（包含该词本身）的key去匹配，看看相关度有多高。 

我们用 q1代表Thinking对应的query vector， k1 及 k2 分别代表Thinking以及Machines对应的key vector，则计算Thinking的attention score的时候我们需要计算k1与k2的点乘，  
同理，我们计算Machines的attention score的时候需要计算q1与 k1,k2的点乘。如上图中所示我们**分别得到了q1与 k1,k2 的点乘积，然后我们进行尺度缩放与softmax归一化**.    
**k和v的来源总是相同的，q在encoder及第一级decoder中与k,v来源相同，在encoder-decoder attention layer中与k,v来源不同**。 








