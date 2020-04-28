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
## reference
[Sparse matrix](https://en.wikipedia.org/wiki/Sparse_matrix)  
[Spark稀疏向量SparseVector类的源码解读](https://blog.csdn.net/qq_26963495/article/details/78858910)
