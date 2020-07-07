## xgboost.plot_importance
xgboost.plot_importance(booster, ax=None, height=0.2, xlim=None, ylim=None, title='Feature importance', xlabel='F score', ylabel='Features', fmap='', importance_type='weight', max_num_features=None, grid=True, show_values=True, **kwargs)

Parameters:
> **importance_type (str, default "weight")**  
–How the importance is calculated: either “weight”, “gain”, or “cover”
```
”weight” is the number of times a feature appears in a tree
”gain” is the average gain of splits which use the feature
”cover” is the average coverage of splits which use the feature where coverage is defined 
        as the number of samples affected by the split
```
> **max_num_features (int, default None)**  
– Maximum number of top features displayed on plot. ***If None, all features will be displayed***.

&nbsp;
## lightgbm.plot_importance
lightgbm.plot_importance(booster, ax=None, height=0.2, xlim=None, ylim=None, title='Feature importance', xlabel='Feature importance', ylabel='Features', importance_type='split', max_num_features=None, ignore_zero=True, figsize=None, dpi=None, grid=True, precision=3, **kwargs)

Parameters类似xgb

&nbsp;
## reference
[Plotting API](https://xgboost.readthedocs.io/en/latest/python/python_api.html#module-xgboost.plotting)  
[lightgbm.plot_importance](https://lightgbm.readthedocs.io/en/latest/pythonapi/lightgbm.plot_importance.html#lightgbm.plot_importance)
