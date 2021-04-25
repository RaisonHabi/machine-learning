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

&nbsp;
## reference
[multiprocessing --- 基于进程的并行](https://docs.python.org/zh-cn/3/library/multiprocessing.html)  
[Forecasting multiple time-series using Prophet in parallel](https://medium.com/spikelab/forecasting-multiples-time-series-using-prophet-in-parallel-2515abd1a245)
