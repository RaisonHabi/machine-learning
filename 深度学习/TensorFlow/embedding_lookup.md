## embedding_lookup
Looks up ids in a list of embedding tensors.
```
tf.nn.embedding_lookup(
    params, ids, max_norm=None, name=None
)
```

### 示例
tf.nn.embedding_lookup函数的用法主要是选取一个张量里面索引对应的元素。  
tf.nn.embedding_lookup（tensor, id）:tensor就是输入张量，id就是张量对应的索引，其他的参数不介绍。

```
import tensorflow as tf;
import numpy as np;
 
c = np.random.random([10,1])
b = tf.nn.embedding_lookup(c, [1, 3])
 
with tf.Session() as sess:
	sess.run(tf.initialize_all_variables())
	print sess.run(b)
	print c
  
输出：
[[ 0.77505197]
 [ 0.20635818]]
[[ 0.23976515]
 [ 0.77505197]
 [ 0.08798201]
 [ 0.20635818]
 [ 0.37183035]
 [ 0.24753178]
 [ 0.17718483]
 [ 0.38533808]
 [ 0.93345168]
 [ 0.02634772]]

分析：输出为张量的第一和第三个元素。
```

&nbsp;
## reference
[tf.nn.embedding_lookup](https://www.tensorflow.org/api_docs/python/tf/nn/embedding_lookup)  
[tf.nn.embedding_lookup函数的用法](https://blog.csdn.net/UESTC_C2_403/article/details/72779417)
