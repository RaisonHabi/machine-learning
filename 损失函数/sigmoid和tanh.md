## Sigmoid和Tanh
### 1.数学上
从数学上看，这两个函数可以通过线性变化等价，唯一的区别在于值域是 (0,1) 和 (-1, 1)。  

**作为激活函数，都存在两端梯度弥散、计算量大的问题**，sigmoid函数因为和生物上的神经元信号刺激的 firing rate 长得像，一度比较流行。。。

**但是，作为非中心对称的激活函数，sigmoid有个问题：输出总是正数**！！！这意味着，神经网络的隐层每个节点的输出（activation）总是正数（注：bias可以看做activation为+1），会导致什么问题？看下图（请忽略粗糙程度 ）：

### 2.图形上

<figure data-size="normal"><noscript><img src="https://pic4.zhimg.com/v2-46df6717d2069fa6ee3706a8d226db76_b.jpg" data-rawwidth="390" data-rawheight="252" data-size="normal" data-caption="" class="content_image" width="390"/></noscript><img src="data:image/svg+xml;utf8,&lt;svg xmlns=&#39;http://www.w3.org/2000/svg&#39; width=&#39;390&#39; height=&#39;252&#39;&gt;&lt;/svg&gt;" data-rawwidth="390" data-rawheight="252" data-size="normal" data-caption="" class="content_image lazy" width="390" data-actualsrc="https://pic4.zhimg.com/v2-46df6717d2069fa6ee3706a8d226db76_b.jpg"/></figure>

图形看上Tanh与Sigmoid差异：
```
1、Sigmoid函数比Tanh函数收敛饱和速度慢

2、Sigmoid函数比Tanh函数梯度收敛更为平滑

3、Sigmoid函数比Tanh函数值域范围更窄
```

### 3.这里就会看到问题，当Sigmoid和Tanh因变量过大时，所有过大的因变量落在函数饱和区间内，函数对因变量的收敛几乎不敏感，这就是为什么在做逻辑回归的时候要对数据先做 Normalize 处理的原因，机器学习教程里很少提到这个点。

&nbsp;
## reference
[在神经网络中，激活函数sigmoid和tanh除了阈值取值外有什么不同吗？ - Daniel的回答 - 知乎](https://www.zhihu.com/question/50396271/answer/220408619)  
[神经网络之激活函数_Sigmoid和Tanh探索](https://zhuanlan.zhihu.com/p/99937331)
