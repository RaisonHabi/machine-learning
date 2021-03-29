## 一、逻辑回归输出的值是真实的概率吗？
### 指数分布族
对于一个随机变量x，只要你确定三个函数，就可以确定一类分布。
这三个函数就是：

h(x)
T(x)
A(η)
η用来确定该类分布的具体参数。
对于我们的伯努利分布，这三个函数是什么样子呢？我们可以从伯努利分布出发，一路变形到与指数分布族一样的形式。

我们看到，伯努利分布确实是指数分布族的一个特殊情况，它的参数Φ与指数分布族中的参数η还有对应关系。
这意味着，我们如果能找到x和η之间的关系，那么也就找到了x和Φ之间的关系。

在这里，我们需要再做一个假设，那就是
η和x之间存在线性的关系！！注意，这是我们做的第二个假设哦。即：
η = θx。
### 总结
**逻辑回归模型之所以是sigmoid 的形式，源于我们假设y服从伯努利分布**，伯努利分布又属于指数分布族，经过推导，将伯努利分布变成指数分布族的形式后。我们发现伯努利分布的唯一参数Φ与指数分布族中的参数η具有sigmoid函数关系，于是我们转而求η与x的关系，此时，我们又假设η与x具有线性关系。
至此，找到了我们要用的模型的样子，也就是逻辑回归。

**回答文章开头的问题，逻辑回归输出的到底是不是概率呢？答案是如果你的情况满足本文所说的两个假设，那么你训练模型的过程，就确实是在对概率进行建模。**

**这两个假设并不是那么容易满足的。所以，很多情况下，我们得出的逻辑回归输出值，无法当作真实的概率，只能作为置信度来使用。**



&nbsp;
## 二、模型校准calibration
### 校准评估
#### 1.1 reliability diagram
#### 1.2 Logarithmic Loss
#### 1.3 Brier score
（详见链接）
### 校准算法
#### 2.1 Platt Calibration
Platt Calibration将模型输出放入逻辑回归中训练，最后将逻辑回归的结果作为模型的 [公式] 校准结果。

要注意的是，为了不引入不必要的偏差，我们训练逻辑回归所用的数据集要不同于训练模型 [公式] 所采用的数据集。原因在于将模型的预测结果拿去训练相当于是对模型的再训练，如果在同一个数据集上再训练很容易导致过拟合。

此外，为了防止过拟合，在逻辑回归预测时，作者建议将标签从 [公式] 改成
#### 2.2 Isotonic Regression
Isotonic Regression，中文名称为保序回归，它并不单单使用于模型校准，在很多其他领域应用都很广泛。算法非常简单，主要作用为将一组无序的数转为有序

&nbsp;
## 三、示例
reliability diagram
```
>>> import numpy as np
>>> from sklearn.calibration import calibration_curve
>>> y_true = np.array([0, 0, 0, 0, 1, 1, 1, 1, 1])
>>> y_pred = np.array([0.1, 0.2, 0.3, 0.4, 0.65, 0.7, 0.8, 0.9,  1.])
>>> prob_true, prob_pred = calibration_curve(y_true, y_pred, n_bins=3)
>>> prob_true
array([0. , 0.5, 1. ])
>>> prob_pred
array([0.2  , 0.525, 0.85 ])
```
Probability calibration with isotonic regression or logistic regression:
```
>>> from sklearn.model_selection import train_test_split
>>> X, y = make_classification(n_samples=100, n_features=2,
...                            n_redundant=0, random_state=42)
>>> X_train, X_calib, y_train, y_calib = train_test_split(
...        X, y, random_state=42
... )
>>> base_clf = GaussianNB()
>>> base_clf.fit(X_train, y_train)
GaussianNB()
>>> calibrated_clf = CalibratedClassifierCV(
...     base_estimator=base_clf,
...     cv="prefit"
... )
>>> calibrated_clf.fit(X_calib, y_calib)
CalibratedClassifierCV(base_estimator=GaussianNB(), cv='prefit')
>>> len(calibrated_clf.calibrated_classifiers_)
1
>>> calibrated_clf.predict_proba([[-0.5, 0.5]])
array([[0.936..., 0.063...]])
```

&nbsp;
## reference
[模型校准calibration](https://zhuanlan.zhihu.com/p/101766505)   
[逻辑回归输出的值是真实的概率吗？](https://www.jianshu.com/p/a8d6b40da0cf)   
[1.16. Probability calibration](https://scikit-learn.org/stable/modules/calibration.html)   
[sklearn.calibration.calibration_curve](https://scikit-learn.org/stable/modules/generated/sklearn.calibration.calibration_curve.html#sklearn.calibration.calibration_curve)   
[sklearn.calibration.CalibratedClassifierCV](https://scikit-learn.org/stable/modules/generated/sklearn.calibration.CalibratedClassifierCV.html#sklearn.calibration.CalibratedClassifierCV)
