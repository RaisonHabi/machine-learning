## 安装
### 1.载压缩文件  
官网（参考2）Download LIBSVM模块下载压缩文件 

&nbsp;

### 2.解压缩
```
$ tar xzvf libsvm-3.24.tar.gz
```

&nbsp;

### 3.编译
> 1)在libsvm目录下执行make  
2)在子目录python下执行make

&nbsp;

### 4.安装
#### 4.1 Anaconda下的Python3
> 拷贝libsvm-3.24/python/目录下的svm.py和svmutil.py到/opt/anaconda3/lib/python3.7/  
拷贝libsvm-3.24/libsvm.so.2到/opt/anaconda3/lib/  
大功告成

#### 4.2 brew下的Python3
将libsvm添加到Python3.7的路径中  
> 首先把libsvm-3.24文件移动到/usr/local/lib/（其他路径也行）  
在usr/local/lib/python3.7/site-packages/下创建pypath.pth路径文件  
编辑pypath.pth文件，加入路径/usr/local/lib/libsvm-3.24/python  
大功告成

&nbsp;

### 5.验证  
```
from svm import *
from svmutil import *

y, x = [1, -1], [{1: 1, 2: 1}, {1: -1, 2: -1}]
prob = svm_problem(y, x)
param = svm_parameter('-t 0 -c 4 -b 1')
model = svm_train(prob, param)
yt = [1]
xt = [{1: 1, 2: 1}]
p_label, p_acc, p_val = svm_predict(yt, xt, model)
print(p_label)
```

&nbsp;

## reference
[MAC的PYTHON3环境下安装LIBSVM](https://bababadboy.github.io/2018/07/27/Mac%E5%AE%89%E8%A3%85libsvm/#)  
[LIBSVM -- A Library for Support Vector Machines](https://www.csie.ntu.edu.tw/~cjlin/libsvm/)
