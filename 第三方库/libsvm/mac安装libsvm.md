## 安装
### 1.下载压缩文件  
官网（参考3）Download LIBSVM模块下载压缩文件 

&nbsp;

### 2.解压缩
```
$ tar xzvf libsvm-3.24.tar.gz
```

&nbsp;

### 3.编译
用命令行 进入到python文件夹(cd libsvm-3.24/python/)，在README文件中可以看到相应的操作；  
即 在当前文件夹下 执行：make lib 命令

&nbsp;

### 4.安装
#### 4.1 Anaconda下的Python3
> 1.拷贝libsvm-3.24/libsvm.so.2 到/opt/anaconda3/lib/python3.7/site-packeages/        
2.在/opt/anaconda3/lib/python3.7/site-packeages/ 下新建libsvm文件夹  
3.拷贝libsvm-3.24/python/目录下的**svm.py**、**svmutil.py**和***commonutil.py*** 到/opt/anaconda3/lib/python3.7/site-packeages/libsvm/ （不要忘记commonutil.py文件）      
4.在/opt/anaconda3/lib/python3.7/site-packeages/libsvm/ 下新建空文件_init_.py  
5.大功告成

#### 4.2 brew下的Python3(未实践过)
将libsvm添加到Python3.7的路径中  
> 首先把libsvm-3.24文件移动到/usr/local/lib/（其他路径也行）  
在usr/local/lib/python3.7/site-packages/下创建pypath.pth路径文件  
编辑pypath.pth文件，加入路径/usr/local/lib/libsvm-3.24/python  
大功告成

&nbsp;

### 5.验证  
```
# from svm import *   这种import会报错（py解释器指定路径下找不到svm这个模块。要标明一下？）
# from svmutil import *
from libsvm.svm import * 
from libsvm.svmutil import *

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
[Mac上安装python的libsvm模块（anaconda环境）](http://www.jeepxie.net/article/718102.html)  
[MAC的PYTHON3环境下安装LIBSVM](https://bababadboy.github.io/2018/07/27/Mac%E5%AE%89%E8%A3%85libsvm/#)  
[LIBSVM -- A Library for Support Vector Machines](https://www.csie.ntu.edu.tw/~cjlin/libsvm/)
