## 一、介绍
FP-growth (Frequent-Pattern growth) 是**一种高效的关联规则学习算法，用于挖掘频繁项集，从而找到数据集中不同元素间的有趣关系。**  
FP-growth 算法通过构建一棵特殊的树状结构——FP-tree（Frequent Pattern Tree），来有效地减少搜索空间并提高挖掘频繁模式的效率。

FP-tree 是一个紧凑的数据结构，它允许在不需要产生候选集的情况下计算频繁项集。以下是 FP-tree 的一些特点：
```
头指针表（Header Table）：这是一个包含所有频繁项及其指向 FP-tree 中第一个相应节点的指针的列表。

条件模式基（Conditional Pattern Base）：这是从数据库中的事务构造出的，每个事务都包含频繁项作为其子集。

FP-tree：这是一个树形结构，用于存储条件模式基中的信息。
树中的每个节点代表一个项目，并且相同项目的节点可以通过链接指针连接在一起。
```

## 二、算法实现
FP-growth 算法的主要步骤包括：
```
1、第一遍扫描数据集，找出所有的频繁项。
2、构建 FP-tree。
3、通过 FP-tree 发现所有的频繁模式。
```
下面是一个简单的 FP-tree 的构建过程示例：假设我们有一个交易记录集合 D，如下所示：
```
Transactions:
T1: {A, B, C, E}
T2: {B, C, E}
T3: {B, E, F}
T4: {C, D, E, F}
T5: {A, C, E}

Frequent items: {A, B, C, D, E, F} （假设所有项都是频繁项）
```
构建 FP-tree 的步骤如下：  
1、首先，根据项的频率降序排序（如果有多个事务，则合并具有相同前缀的事务），并构建初始 FP-tree。
```
  Root
   |
   A(1) -- C(3) -- E(5)
           \      \
            B(3)  \
             \     F(2)
              D(1)
```
在这个 FP-tree 中，根节点没有实际意义，它只是作为所有路径的起点。每个节点上的数字表示该路径出现的次数。

2、接下来，可以使用 FP-tree 来挖掘频繁模式。例如，如果我们要查找所有包含 E 的频繁模式，我们可以从头指针表开始，跟踪指向 E 节点的所有指针，并递归地探索树的结构来发现所有可能的模式。

FP-growth 算法的一个主要优点是它的高效性，因为它只需要两次数据库扫描，并且在内存中维护了一个紧凑的数据结构，这使得频繁模式的挖掘变得更加高效。此外，它避免了传统 Apriori 算法中的大量候选集生成和测试过程，从而大大减少了计算量。

## references
通义千问  
[FP Tree算法原理总结](https://www.cnblogs.com/pinard/p/6307064.html)
