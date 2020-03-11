## 模型和参数
**XGBoost模型objective说明**：  
**“multi:softmax”** –- 让XGBoost采用softmax目标函数处理多分类问题，同时需要设置参数num_class（类别个数）  
**“multi:softprob”** –- 和softmax一样，但是输出的是ndata * nclass的向量，可以将该向量reshape成ndata行nclass列的矩阵。输出每（列）个数据表示样本所属于每个类别的概率。
## 预测输出
在多分类预测模型下，预测输出分为两种情况：  
> 一是直接输出分类，参数’objective’为’multi:softmax’；  
另一种是输出各个分类的概率，参数’objective’为’multi:softprob’。  

本案例使用输出各个分类概率，贴近实际工作场景，如出现一种工况，计算机给出“供液不足”的概率是78%、“气影响”的概率是21%、其他…。

通过取出最大概率的分类，则可以为预测分析结果，如下代码所示：
```
y_pred=model.predict(xgb.DMatrix(X_test))
yprob = np.argmax(y_pred, axis=1)  # return the index of the biggest pro
```
注：训练模型时，输入的标签（Lable）为单列分类数字，例如本案例分类为[0,1,2,3]，通过代码中“np.argmax(y_pred, axis=1)”取到最大概率值的位置为结果。
## reference
[应用XGboost实现多分类模型实践](https://flashgene.com/archives/30247.html)  
[XGBOOST从原理到实战：二分类 、多分类](https://blog.csdn.net/HHTNAN/article/details/81079257#%E5%A4%9A%E5%88%86%E7%B1%BB)
