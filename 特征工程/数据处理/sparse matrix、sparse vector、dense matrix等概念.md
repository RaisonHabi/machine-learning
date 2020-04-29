## Sparse matrix
In numerical analysis and scientific computing, **a sparse matrix or sparse array is a matrix in which most of the elements are zero**.   
By contrast, if most of the elements are nonzero, then the matrix is considered dense. 

The number of zero-valued elements divided by the total number of elements (e.g., m × n for an m × n matrix) is called the **sparsity** of the matrix (which is equal to 1 minus the density of the matrix).   
Using those definitions, a matrix will be sparse when its sparsity is greater than 0.5.

&nbsp;
## sparse vector
稀疏向量，底层基于索引数组和值数组共同实现.该类的核心思想是用两个数组，一个记录原始向量中非零元素的值，另一个记录原始向量中非零元素在原始向量中的位置。  
一共有三个数据成员，size记录原始向量的长度，indices数组为索引数组，values数组为值数组，索引数组和值数组的长度必须一致。  
注意：一个普通的SparseVector向量和普通向量没有区别，只有在这个向量调用了该类的toSparse方法把向量本身做了压缩之后值数组才只是存储非零元素。
下面是该类的源码，我在关键的地方都做了详细注释。


&nbsp;
## 易混淆/不严谨的表述
感觉网上一些资料表述不清晰，容易引起混淆，比如[将稠密矩阵转化为稀疏矩阵](https://blog.csdn.net/Guo_ya_nan/article/details/101371834)一文。  
准确的描述参考[Sparse matrices (scipy.sparse)](https://docs.scipy.org/doc/scipy/reference/sparse.html)    


**示例：**  
```
import numpy as np
from scipy import sparse

A = np.array([[1,2,0],[0,0,3],[1,0,4]])

# csr_matrix: Compressed Sparse Row format 压缩的稀疏行格式
sA = sparse.csr_matrix(A)
```
```
print(type(A))
print(A)

#输出
<class 'numpy.ndarray'>
[[1 2 0]
 [0 0 3]
 [1 0 4]]
```
```
print(sA)
print(type(sA))

#输出
##（行index，列index）value值
  (0, 0)	1
  (0, 1)	2
  (1, 2)	3
  (2, 0)	1
  (2, 2)	4
<class 'scipy.sparse.csr.csr_matrix'>
```
```
# csc_matrix: Compressed Sparse Column format 压缩的稀疏列格式
sa=sparse.csc_matrix(A)
print(sa)
print(type(sa))

#输出
##（行index，列index）value值
  (0, 0)	1
  (2, 0)	1
  (0, 1)	2
  (1, 2)	3
  (2, 2)	4
<class 'scipy.sparse.csc.csc_matrix'>
```
```
##todense 应该是恢复密集表示，即恢复压缩前的矩阵的表示形式，而非转化为密集矩阵（因为原矩阵可能是稀疏矩阵，
##也可能是密集矩阵，稀疏还是密集的定义是0元素个数占总元素个数的比例）
da=sA.todense()    
print(da)
print(type(da))

#输出
[[1 2 0]
 [0 0 3]
 [1 0 4]]
<class 'numpy.matrix'>
```

&nbsp;

一个模型文件要上线的话，一定会力求精简，而稀疏矩阵由于其庞大的内存占用，而会首当其中被改写，所幸修改的逻辑不是很复杂，下面简要介绍下修改逻辑。  
**1.三元组**  
三元组是最直观且最省力的做法，只需要修改存储的结构即可。三元组的结构就是<row,col,value>  

  **2.压缩存储**  
  > compressed sparse row(CSR)  
  compressed sparse col(CSC)
  



&nbsp;
## todense
[scipy.sparse.csr_matrix.todense](https://docs.scipy.org/doc/scipy0.19.0/reference/generated/scipy.sparse.csr_matrix.todense.html)  

csr_matrix.todense(order=None, out=None)  
Return a dense matrix representation of this matrix.  

Returns:	  
arr : numpy.matrix, 2-dimensional  
A NumPy matrix object with the same shape and containing the same data represented by the sparse matrix, with the requested memory order. If out was passed and was an array (rather than a numpy.matrix), it will be filled with the appropriate values and returned wrapped in a numpy.matrix object that shares the same memory.

&nbsp;
## 应用
[干货 | 机器学习-稀疏矩阵的处理](https://zhuanlan.zhihu.com/p/55029285)该文对稀疏、密集的个别定义虽然稍模糊，但是有很多不错的应用方法

&nbsp;
## reference
[Sparse matrix](https://en.wikipedia.org/wiki/Sparse_matrix)  
[Spark稀疏向量SparseVector类的源码解读](https://blog.csdn.net/qq_26963495/article/details/78858910)
