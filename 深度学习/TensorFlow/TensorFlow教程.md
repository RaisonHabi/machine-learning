## 
TF2 runs Eager Execution by default, thus removing the need for Sessions.   
If you want to run static graphs, the more proper way is to use tf.function() in TF2.   
While Session can still be accessed via tf.compat.v1.Session() in TF2, I would discourage using it.   
It may be helpful to demonstrate this difference by comparing the difference in hello worlds:

TF1.x hello world:
```
import tensorflow as tf
msg = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(msg))
```
TF2.x hello world:
```
import tensorflow as tf
msg = tf.constant('Hello, TensorFlow!')
tf.print(msg)
```

&nbsp;
## reference
[TF1_TF2](https://stackoverflow.com/questions/55142951/tensorflow-2-0-attributeerror-module-tensorflow-has-no-attribute-session)  
[TensorFlow教程](http://c.biancheng.net/view/1881.html)
