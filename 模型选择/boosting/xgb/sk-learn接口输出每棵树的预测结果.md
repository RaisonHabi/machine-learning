
## predict
**predict(data, output_margin=False, ntree_limit=None, validate_features=True, base_margin=None)**  
Predict with data.

Note:
> This function is not thread safe.  
For each booster object, predict can only be called from one thread. If you want to run prediction using multiple thread, call xgb.copy() to make copies of model object and then call predict().
```
preds = bst.predict(dtest, ntree_limit=num_round)
```
### Parameters
**data (numpy.array/scipy.sparse)** – Data to predict with  
**output_margin (bool)** – Whether to output the raw untransformed margin value.  
**ntree_limit (int)** – Limit number of trees in the prediction; defaults to best_ntree_limit if defined (i.e. it has been trained with early stopping), otherwise 0 (use all trees).  
**validate_features (bool)** – When this is True, validate that the Booster’s and data’s feature_names are identical. Otherwise, it is assumed that the feature_names are the same.

### Returns
prediction

### Return type
numpy array
## code示例
```
# 输入一条样本，输出每棵树的预测结果
def print_every_tree_predict(dtest, xgbm, num_round):
    sum_pre = 0.0
    tree_predict_list = []
    for i in range(1,num_round+1):
        ypred = xgbm.predict(xgb.DMatrix(dtest), ntree_limit=i)[0]
        
        curr_tree_predict = -np.log((1/ypred)-1)
        
        if sum_pre>0.0:
            pre_tree_predict = -np.log((1/sum_pre)-1)
        else:
            pre_tree_predict = 0.0
            
#         print(pre_tree_predict)
        
        final_pre = round(curr_tree_predict-pre_tree_predict,6)
            
        tree_predict_list.append('no %s tree: %f' % (i-1,final_pre))
        
        sum_pre = ypred
    return tree_predict_list
```

## reference
[predict](https://xgboost.readthedocs.io/en/latest/python/python_api.html#xgboost.XGBClassifier.predict)
