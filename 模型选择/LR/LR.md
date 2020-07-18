## lr模型属性coef_和intercept_
g(x) = w1x1 + w2x2 + w3x3 + w4x4 + w0
```
系数 coef_: ndarray of shape (1, n_features) or (n_classes, n_features)
Coefficient of the features in the decision function.
coef_ is of shape (1, n_features) when the given problem is binary. 
In particular, when multi_class='multinomial', coef_ corresponds to outcome 1 (True) and -coef_ corresponds to outcome 0 (False).

截距 intercept_: ndarray of shape (1,) or (n_classes,)
Intercept (a.k.a. bias) added to the decision function.
If fit_intercept is set to False, the intercept is set to zero. intercept_ is of shape (1,) when the given problem is binary. 
In particular, when multi_class='multinomial', intercept_ corresponds to outcome 1 (True) and -intercept_ corresponds to outcome 0 (False).
```

&nbsp;
## reference
[sklearn.linear_model.LogisticRegression](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html)  
[sklearn中的coef_和intercept_](https://www.jianshu.com/p/6a818b53a37e)
