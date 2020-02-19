## sklearn接口的xgboost 分类器  
xgboost.XGBClaaifier()  

**xgboost.fit(x,y)**

> xgboost.predict(test_x)    

> xgboost.predict_proba(test_x)

> xgboost.score(test_x,y)

讨论：  
结果1“可能”不准，因为predict默认采用0.5做阈值；另外，score也是利用0.5做阈值计算的分数。  
而如果根据predict_proba，我们分析不同阈值下的正确率分数，会发现在某些阈值下，正确率分数会更高。


## 非sklearn接口的xgboost分类器  
**xgboost.train()**

xgboost.predict() **只会输出概率或sigmoid转换前的贡献度**model_a.predict(pred_contribs= True)，需要人工指定阈值，才能做分类。

 
## reference
[xgboost: predict 和 predict_proba 分类器正确率计算的讨论](https://blog.csdn.net/aaa_aaa1sdf/article/details/83024776)
