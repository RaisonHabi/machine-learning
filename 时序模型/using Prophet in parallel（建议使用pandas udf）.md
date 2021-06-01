## multiprocessing
multiprocessing 是一个支持使用与 threading 模块类似的 API 来产生进程的包。 multiprocessing 包同时提供了本地和远程并发操作，通过使用子进程而非线程有效地绕过了 全局解释器锁。 因此，multiprocessing 模块允许程序员充分利用给定机器上的多个处理器。 它在 Unix 和 Windows 上均可运行。
### Pool 对象
multiprocessing 模块还引入了在 threading 模块中没有的API。一个主要的例子就是 Pool 对象，它提供了一种快捷的方法，赋予函数并行化处理一系列输入值的能力，可以将输入数据分配给不同进程处理（数据并行）。下面的例子演示了在模块中定义此类函数的常见做法，以便子进程可以成功导入该模块。这个数据并行的基本例子使用了 Pool ，
```
from multiprocessing import Pool

def f(x):
    return x*x

if __name__ == '__main__':
    with Pool(5) as p:
        print(p.map(f, [1, 2, 3]))
```
```
[1, 4, 9]

```
### Process 类
在 multiprocessing 中，通过创建一个 Process 对象然后调用它的 start() 方法来生成进程。 Process 和 threading.Thread API 相同。 一个简单的多进程程序示例是:
```
from multiprocessing import Process

def f(name):
    print('hello', name)

if __name__ == '__main__':
    p = Process(target=f, args=('bob',))
    p.start()
    p.join()
```
### 示例
```
from multiprocessing import cpu_count, Pool
import numpy as np
import time

# cpu 数量
cores = cpu_count()
# 分块个数
partitions = cores
print("cores' num: %d" % partitions)

def parallelize(data, func):
    """
    多核并行处理模块
    :param df: DataFrame数据
    :param func: 预处理函数
    :return: 处理后的数据
    """
    # 数据切分
    data_split = np.array_split(data, partitions) # 这里把数据按照核心数N，分成N份
    # 进程池
    pool = Pool(cores)
    # 数据分发 合并
    data = pool.map(func, data_split) # 把数据，和要处理数据的函数，分别导入对应的进程池
    # 关闭进程池
    pool.close()
    # 执行完close后不会有新的进程加入到pool,join函数等待所有子进程结束
    pool.join()
    return data

def double(num):
    time.sleep(5) # 休眠 5 秒
    return num*2

data_raw = list(range(16))

time_start = time.time()

results = parallelize(data_raw,double)

time_end = time.time()
print('results:',results)
print('process time:',time_end-time_start)

```
运行结果：
```
cores' num: 4
results: [array([0, 2, 4, 6]), array([ 8, 10, 12, 14]), array([16, 18, 20, 22]), array([24, 26, 28, 30])]
process time: 5.113263130187988
```

&nbsp;
## References
[multiprocessing --- 基于进程的并行](https://docs.python.org/zh-cn/3/library/multiprocessing.html)  
[Python的并行处理模块multiprocessing](https://blog.csdn.net/qq_42067550/article/details/106129902)   
[Forecasting multiple time-series using Prophet in parallel](https://medium.com/spikelab/forecasting-multiples-time-series-using-prophet-in-parallel-2515abd1a245)
