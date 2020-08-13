## gensim：topic modelling for humans 
Gensim是一个开源库，**使用现代统计机器学习来进行无监督的主题建模和自然语言处理**。  
Gensim是用Python和Cython实现的。   
Gensim旨在**使用数据流和增量在线算法处理大型文本集合**，这使其有别于仅针对内存中处理的大多数其他机器学习软件包。

&nbsp;
## 实战示例
[NLP自然语言处理库系列教程——gensim库](https://blog.csdn.net/luanpeng825485697/article/details/80452334)

<h2>gensim简介</h2><p>作为自然语言处理爱好者，大家都应该听说过或使用过大名鼎鼎的Gensim吧，这是一款具备多种功能的神器。<br/>Gensim是一款开源的第三方Python工具包，用于从原始的非结构化的文本中，无监督地学习到文本隐层的主题向量表达。<br/>它支持包括TF-IDF，LSA，LDA，和word2vec在内的多种主题模型算法，<br/>支持流式训练，并提供了诸如相似度计算，信息检索等一些常用任务的API接口</p><h2>基本概念</h2><ul><li>语料（Corpus）：一组原始文本的集合，用于无监督地训练文本主题的隐层结构。语料中不需要人工标注的附加信息。在Gensim中，Corpus通常是一个可迭代的对象（比如列表）。每一次迭代返回一个可用于表达文本对象的稀疏向量。</li><li>向量（Vector）：由一组文本特征构成的列表。是一段文本在Gensim中的内部表达。</li><li>稀疏向量（SparseVector）：通常，我们可以略去向量中多余的0元素。此时，向量中的每一个元素是一个(key, value)的元组</li><li>模型（Model）：是一个抽象的术语。定义了两个向量空间的变换（即从文本的一种向量表达变换为另一种向量表达）。</li></ul><h2>步骤一：训练语料的预处理</h2><p>由于Gensim使用python语言开发的，为了减少安装中的繁琐，直接使用anaconda工具进行集中安装，<br/>输入：pip install gensim，这里不再赘述。</p><p>训练语料的预处理指的是将文档中原始的字符文本转换成Gensim模型所能理解的稀疏向量的过程。</p><p>通常，我们要处理的原生语料是一堆文档的集合，每一篇文档又是一些原生字符的集合。在交给Gensim的模型训练之前，我们需要将这些原生字符解析成Gensim能处理的稀疏向量的格式。由于语言和应用的多样性，我们需要先对原始的文本进行分词、去除停用词等操作，得到每一篇文档的特征列表。例如，在词袋模型中，文档的特征就是其包含的word：</p><div class="highlight"><pre><code class="language-text">texts = [[&#39;human&#39;, &#39;interface&#39;, &#39;computer&#39;],
[&#39;survey&#39;, &#39;user&#39;, &#39;computer&#39;, &#39;system&#39;, &#39;response&#39;, &#39;time&#39;],
[&#39;eps&#39;, &#39;user&#39;, &#39;interface&#39;, &#39;system&#39;],
[&#39;system&#39;, &#39;human&#39;, &#39;system&#39;, &#39;eps&#39;],
[&#39;user&#39;, &#39;response&#39;, &#39;time&#39;],
[&#39;trees&#39;],
[&#39;graph&#39;, &#39;trees&#39;],
[&#39;graph&#39;, &#39;minors&#39;, &#39;trees&#39;],
[&#39;graph&#39;, &#39;minors&#39;, &#39;survey&#39;]]
</code></pre></div><p>其中，corpus的每一个元素对应一篇文档。</p><p>接下来，我们可以调用Gensim提供的API建立语料特征（此处即是word）的索引字典，并将文本特征的原始表达转化成词袋模型对应的稀疏向量的表达。依然以词袋模型为例：</p><div class="highlight"><pre><code class="language-text">from gensim import corpora
dictionary = corpora.Dictionary(texts)
corpus = [dictionary.doc2bow(text) for text in texts]
print corpus[0] # [(0, 1), (1, 1), (2, 1)]
</code></pre></div><p>到这里，训练语料的预处理工作就完成了。我们得到了语料中每一篇文档对应的稀疏向量（这里是bow向量）；向量的每一个元素代表了一个word在这篇文档中出现的次数。值得注意的是，虽然词袋模型是很多主题模型的基本假设，这里介绍的doc2bow函数并不是将文本转化成稀疏向量的唯一途径。在下一小节里我们将介绍更多的向量变换函数。</p><p>最后，出于内存优化的考虑，Gensim支持文档的流式处理。我们需要做的，只是将上面的列表封装成一个Python迭代器；每一次迭代都返回一个稀疏向量即可。</p><div class="highlight"><pre><code class="language-text">class MyCorpus(object):
def __iter__(self):
    for line in open(&#39;mycorpus.txt&#39;):
        # assume there&#39;s one document per line, tokens                   separated by whitespace
        yield dictionary.doc2bow(line.lower().split())
</code></pre></div><h2>步骤二：主题向量的变换</h2><p>对文本向量的变换是Gensim的核心。通过挖掘语料中隐藏的语义结构特征，我们最终可以变换出一个简洁高效的文本向量。</p><p>在Gensim中，每一个向量变换的操作都对应着一个主题模型，例如上一小节提到的对应着词袋模型的doc2bow变换。每一个模型又都是一个标准的Python对象。下面以TF-IDF模型为例，介绍Gensim模型的一般使用方法。</p><p>首先是模型对象的初始化。通常，Gensim模型都接受一段训练语料（注意在Gensim中，语料对应着一个稀疏向量的迭代器）作为初始化的参数。显然，越复杂的模型需要配置的参数越多。</p><div class="highlight"><pre><code class="language-text">from gensim import models
tfidf = models.TfidfModel(corpus)</code></pre></div><p>其中，corpus是一个返回bow向量的迭代器。这两行代码将完成对corpus中出现的每一个特征的IDF值的统计工作。</p><p>接下来，我们可以调用这个模型将任意一段语料（依然是bow向量的迭代器）转化成TFIDF向量（的迭代器）。需要注意的是，这里的bow向量必须与训练语料的bow向量共享同一个特征字典（即共享同一个向量空间）。</p><div class="highlight"><pre><code class="language-text">doc_bow = [(0, 1), (1, 1)]
print tfidf[doc_bow] # [(0, 0.70710678), (1, 0.70710678)]
</code></pre></div><p>注意，同样是出于内存的考虑，model[corpus]方法返回的是一个迭代器。如果要多次访问model[corpus]的返回结果，可以先将结果向量序列化到磁盘上。</p><p>我们也可以将训练好的模型持久化到磁盘上，以便下一次使用：</p><div class="highlight"><pre><code class="language-text">tfidf.save(&#34;./model.tfidf&#34;)
tfidf = models.TfidfModel.load(&#34;./model.tfidf&#34;)
</code></pre></div><p>Gensim内置了多种主题模型的向量变换，包括LDA，LSI，RP，HDP等。这些模型通常以bow向量或tfidf向量的语料为输入，生成相应的主题向量。所有的模型都支持流式计算。关于Gensim模型更多的介绍，可以参考这里<a href="https://link.zhihu.com/?target=https%3A//radimrehurek.com/gensim/apiref.html" class=" wrap external" target="_blank" rel="nofollow noreferrer">：API Reference</a></p><h2>步骤三：文档相似度的计算</h2><p>在得到每一篇文档对应的主题向量后，我们就可以计算文档之间的相似度，进而完成如文本聚类、信息检索之类的任务。在Gensim中，也提供了这一类任务的API接口。</p><p>以信息检索为例。对于一篇待检索的query，我们的目标是从文本集合中检索出主题相似度最高的文档。</p><p>首先，我们需要将待检索的query和文本放在同一个向量空间里进行表达（以LSI向量空间为例）：</p><div class="highlight"><pre><code class="language-text"># 构造LSI模型并将待检索的query和文本转化为LSI主题向量
# 转换之前的corpus和query均是BOW向量
lsi_model = models.LsiModel(corpus, id2word=dictionary,          num_topics=2)
documents = lsi_model[corpus]
query_vec = lsi_model[query]
</code></pre></div><p>接下来，我们用待检索的文档向量初始化一个相似度计算的对象：</p><div class="highlight"><pre><code class="language-text">index = similarities.MatrixSimilarity(documents)
</code></pre></div><p>我们也可以通过save()和load()方法持久化这个相似度矩阵：</p><div class="highlight"><pre><code class="language-text">index.save(&#39;/tmp/test.index&#39;)
index = similarities.MatrixSimilarity.load(&#39;/tmp/test.index&#39;)
</code></pre></div><p>注意，如果待检索的目标文档过多，使用similarities.MatrixSimilarity类往往会带来内存不够用的问题。此时，可以改用similarities.Similarity类。二者的接口基本保持一致。</p><p>最后，我们借助index对象计算任意一段query和所有文档的（余弦）相似度：</p><div class="highlight"><pre><code class="language-text">sims = index[query_vec] 
#返回一个元组类型的迭代器：(idx, sim)</code></pre></div><h2>常见主题模型总结</h2><h2>TF-IDF</h2><p>TF-IDF（注意：这里不是减号）是一种统计方法，用以评估一字词对于一个文件集或一个语料库中的其中一份文件的重要程度。<br/>字词的重要性随着它在文件中出现的次数成正比增加，但同时会随着它在语料库中出现的频率成反比下降。TF-IDF加权的各种形式常被搜索引擎应用，作为文件与用户查询之间相关程度的度量或评级。<br/>1. 一个词预测主题能力越强，权重就越大，反之，权重就越小。我们在网页中看到“原子能”这个词，或多或少地能了解网页的主题。我们看到“应用”一次，对主题基本上还是一无所知。因此，“原子能“的权重就应该比应用大。<br/>2. 应删除词的权重应该是零。</p><h2>LDA文档主题生成模型</h2><p>LDA是一种文档主题生成模型，包含词、主题和文档三层结构。</p><p>所谓生成模型，就是说，我们认为一篇文章的每个词都是通过“以一定概率选择了某个主题，并从这个主题中以一定概率选择某个词语”这样一个过程得到。文档到主题服从多项式分布，主题到词服从多项式分布。</p><p>LDA是一种非监督机器学习技术，可以用来识别大规模文档集或语料库中潜藏的主题信息。它采用了词袋的方法，这种方法将每一篇文档视为一个词频向量，从而将文本信息转化为了易于建模的数字信息。</p><p>但是词袋方法没有考虑词与词之间的顺序，这简化了问题的复杂性，同时也为模型的改进提供了契机。每一篇文档代表了一些主题所构成的一个概率分布，而每一个主题又代表了很多单词所构成的一个概率分布。</p>

&nbsp;
## reference
[gensim](https://radimrehurek.com/gensim/apiref.html)  
[15分钟入门Gensim](https://zhuanlan.zhihu.com/p/37175253).  
[NLP自然语言处理库系列教程——gensim库](https://blog.csdn.net/luanpeng825485697/article/details/80452334)
