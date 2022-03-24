## xgb原生接口
### 1、xgb.to_graphviz(model,num_trees=0,rankdir='LR')
有些参数可以调整, 比如num_trees=0表示画第一棵树, rankdir='LR'表示图片是从左到右(Left to Right).

### 2、xgb.plot_tree(model,num_trees=0,rankdir='LR')

### 3、保存图片
```
fig = pyplot.gcf()
fig.set_size_inches(150, 100)
fig.savefig('tree.png')
fig.show()
```

## References
[如何画XGBoost里面的决策树(decision tree)](https://zhuanlan.zhihu.com/p/28324798)
