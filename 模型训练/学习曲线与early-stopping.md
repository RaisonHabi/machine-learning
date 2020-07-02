## 背景
1.在使用XGBoost模型时如何监控训练过程中模型的表现，如何绘制学习曲线 ；   
2.如何使用Early Stop方法在模型表现最好的时候停止训练
## 在XGBoost中监控模型的表现
XGBoost模型在训练时可以计算并输入在某个指定的测试数据集的性能表现。

在调用model.fit()函数时，可以指定测试数据集和评价指标，同时设置verbose参数为True，这样就可以在训练过程中输出模型在测试集的表现。

例如，我们可以通过下面的方法在使用XGBoost训练二分类任务时输出分类错误率（通过“error”指定）：
```
eval_set = [(X_test, y_test)]
model.fit(X_train, y_train, eval_metric="error", eval_set=eval_set, verbose=True)
```
模型在各个数据集上的表现可以在训练结束后通过model.evals_result()函数获取，这个函数返回一个dict包含了评估数据集的代码和对应的分数列表，例如：
```
results = model.evals_result()
print(results)
```
这将输出如下的结果：
```
{
	'validation_0': {'error': [0.259843, 0.26378, 0.26378, ...]},
	'validation_1': {'error': [0.22179, 0.202335, 0.196498, ...]}
}
```
下面是一段完整的代码，展示了如何将收集到的数据绘制成学习曲线：
```
from matplotlib import pyplot

results = model.evals_result()
epochs = len(results['validation_0']['error'])
x_axis = range(0, epochs)

# plot log loss
fig, ax = pyplot.subplots()
ax.plot(x_axis, results['validation_0']['logloss'], label='Train')
ax.plot(x_axis, results['validation_1']['logloss'], label='Test')
ax.legend()
pyplot.ylabel('Log Loss')
pyplot.title('XGBoost Log Loss')
pyplot.show()

# plot classification error
fig, ax = pyplot.subplots()
ax.plot(x_axis, results['validation_0']['error'], label='Train')
ax.plot(x_axis, results['validation_1']['error'], label='Test')
ax.legend()
pyplot.ylabel('Classification Error')
pyplot.title('XGBoost Classification Error')
pyplot.show()
```

&nbsp;
## 在XGBoost中进行Early Stop
XGBoost提供了在指定轮数完成后提前停止训练的功能。

除了提供用于评估每轮迭代中的评价指标和数据集之外，还需要指定一个窗口大小，意味着连续这么多轮迭代中模型的效果没有提升。这是通过early_stopping_rounds参数来设置的。

例如，我们可以像下面这样设置连续10轮中对数损失都没有提升：
```
eval_set = [(X_test, y_test)]
model.fit(X_train, y_train, early_stopping_rounds=10, eval_metric="logloss", eval_set=eval_set, verbose=True)
```

&nbsp;
## 总结
通常将early_stopping_rounds设置为一个与总训练轮数相关的函数（本例中是10%），  
或者通过观察学习曲线来设置使得训练过程包含拐点，这两种方法都是不错的选择。

&nbsp;
## reference
[在XGBoost中通过Early Stop避免过拟合](https://blog.csdn.net/MiMicoa/article/details/85019163)  
[Avoid Overfitting By Early Stopping With XGBoost In Python](https://machinelearningmastery.com/avoid-overfitting-by-early-stopping-with-xgboost-in-python/)
