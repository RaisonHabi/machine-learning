## Data Interface
The XGBoost python module is able to load data from:
> LibSVM text format file   
Comma-separated values (CSV) file  
NumPy 2D array  
SciPy 2D sparse array  
cuDF DataFrame    
**Pandas data frame**, and  
XGBoost binary buffer file

&nbsp;
## The data is stored in a DMatrix object
**To load a libsvm text file or a XGBoost binary file into DMatrix**:  
```
dtrain = xgb.DMatrix('train.svm.txt')  
dtest = xgb.DMatrix('test.svm.buffer')
```

**To load a CSV file into DMatrix**:   
label_column指定标签列；一般要求csv文件已处理好特征，无需进行数据预处理？
```
#label_column specifies the index of the column containing the true label  
dtrain = xgb.DMatrix('train.csv?format=csv&label_column=0')  
dtest = xgb.DMatrix('test.csv?format=csv&label_column=0')  
```

**To load a Pandas data frame into DMatrix:**   
```
data = pandas.DataFrame(np.arange(12).reshape((4,3)), columns=['a', 'b', 'c'])  
label = pandas.DataFrame(np.random.randint(2, size=4))  
dtrain = xgb.DMatrix(data, label=label)
```

其他格式详见参考

&nbsp;
## reference
[Python Package Introduction](https://xgboost.readthedocs.io/en/latest/python/python_intro.html)  
[Python xgboost.DMatrix() Examples](https://www.programcreek.com/python/example/99828/xgboost.DMatrix)
