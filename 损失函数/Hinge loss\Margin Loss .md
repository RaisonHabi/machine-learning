## Hinge Loss
在机器学习中，hinge loss作为一个损失函数(loss function)，通常被用于最大间隔算法(maximum-margin)    
而最大间隔算法又是SVM(支持向量机support vector machines)用到的重要算法   
 
注意：SVM的学习算法有两种解释  
> 1.间隔最大化与拉格朗日对偶  
2.Hinge Loss

&nbsp;

<p>Hinge损失函数标准形式如下：</p><p><img src="https://www.zhihu.com/equation?tex=L%28y%2C+f%28x%29%29+%3D+max%280%2C+1-yf%28x%29%29+++%5C%5C" alt="L(y, f(x)) = max(0, 1-yf(x))   \\" eeimg="1"/> </p>

<figure><noscript><img src="https://pic1.zhimg.com/50/ca04dacb00820a92e39b1045159204c4_hd.jpg?source=1940ef5c" data-rawwidth="750" data-rawheight="574" class="origin_image zh-lightbox-thumb" width="750" data-original="https://pic3.zhimg.com/ca04dacb00820a92e39b1045159204c4_r.jpg?source=1940ef5c"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;750&#39; height=&#39;574&#39;&gt;&lt;/svg&gt;" data-rawwidth="750" data-rawheight="574" class="origin_image zh-lightbox-thumb lazy" width="750" data-original="https://pic3.zhimg.com/ca04dacb00820a92e39b1045159204c4_r.jpg?source=1940ef5c" data-actualsrc="https://pic1.zhimg.com/50/ca04dacb00820a92e39b1045159204c4_hd.jpg?source=1940ef5c"/></figure><br/>

&nbsp;
## 铰接/合页 损失
是一个用于训练分类器的损失函数。图像见参考   
铰链损失常被用于“最大间隔分类”，尤其适用于支持向量机。    
对于一个预期输出t = ±1，分类结果y 的铰接损失定义为 注意 y 应该是分类器的“原始输出”，而非预测标签。  
例如，在线性支持向量机中，其中 是超平面参数，是需要分类的点。

&nbsp;
## reference
[一文搞懂支持向量机(SVM)算法](https://zhuanlan.zhihu.com/p/52168498).  
[常见的损失函数(loss function)总结](https://zhuanlan.zhihu.com/p/58883095).  
[怎么样理解SVM中的hinge-loss？ - Slumbers的回答 - 知乎](https://www.zhihu.com/question/47746939/answer/570590275).  
[怎么样理解SVM中的hinge-loss？ - 知乎用户的回答 - 知乎](https://www.zhihu.com/question/47746939/answer/125001776).  
[Hinge loss](https://blog.csdn.net/hustqb/article/details/78347713)  
[Margin Loss 损失函数的设计](https://www.cnblogs.com/lainey/p/8583101.html)
