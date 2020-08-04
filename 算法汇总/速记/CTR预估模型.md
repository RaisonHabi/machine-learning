### 点击率（CTR,Click-through rate）预估模型 
在传统的线性模型如LR中，每个特征都是独立的，如果需要考虑特征与特征直接的交互作用，可能需要人工对特征进行交叉组合； 

非线性SVM可以对特征进行kernel映射，但是在特征高度稀疏的情况下，并不能很好地进行学习；

现在也有很多分解模型Factorization model如矩阵分解MF、SVD++等，这些模型可以学习到特征之间的交互隐藏关系，但基本上每个模型都只适用于特定的输入和场景。 

为此，在高度稀疏的数据场景下如推荐系统，FM（Factorization Machine）出现了。 

Field-aware Factorization Machines(FFM): 

DeepFM算法有效的结合了因子分解机与神经网络在特征学习中的优点：同时提取到低阶组合特征与高阶组合特征
