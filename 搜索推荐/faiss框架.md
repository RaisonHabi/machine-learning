## 简介
**faiss是为稠密向量提供高效相似度搜索和聚类的框架**。由Facebook AI Research研发。 支持十亿级别向量的搜索，是目前最为成熟的近似近邻搜索库。
具有以下特性。
```
1、提供多种检索方法
2、速度快
3、可存在内存和磁盘中
4、C++实现，提供Python封装调用。
5、大部分算法支持GPU实现
```
### Faiss核心算法实现
Faiss对一些基础的算法提供了非常高效的失效
```
聚类Faiss提供了一个高效的k-means实现
PCA降维算法
PQ(ProductQuantizer)编码/解码
```

&nbsp;
## Quick Start
详见参考文档

&nbsp;
## reference
[faiss简介及示例](https://blog.csdn.net/kanbuqinghuanyizhang/article/details/80774609)   
[FAISS 用法](https://zhuanlan.zhihu.com/p/40236865).  
[Faiss流程与原理分析](https://www.cnblogs.com/yhzhou/p/10568728.html)
