## 数据格式
XGBoost可以加载多种数据格式的训练数据：  　　
> libsvm 格式的文本数据；  
Numpy 的二维数组；  
XGBoost 的二进制的缓存文件。加载的数据存储在对象 DMatrix 中。  

示例：  
> **加载libsvm格式的数据**:  
dtrain1 = xgb.DMatrix('train.svm.txt')

> **加载二进制的缓存文件**:    
dtrain2 = xgb.DMatrix('train.svm.buffer')

> **加载numpy的数组**:  
data = np.random.rand(5,10) #5 entities, each contains 10 features  
label = np.random.randint(2, size=5) #binary target  
dtrain = xgb.DMatrix( data, label=label)

> **将scipy.sparse格式的数据转化为 DMatrix 格式**:  
csr = scipy.sparse.csr_matrix( (dat, (row,col)) )  
dtrain = xgb.DMatrix( csr ) 

> **将 DMatrix 格式的数据保存成XGBoost的二进制格式，在下次加载时可以提高加载速度，使用方式如下**    
dtrain = xgb.DMatrix('train.svm.txt')  
dtrain.save_binary("train.buffer")

> **可以用如下方式处理 DMatrix中的缺失值**：  
dtrain = xgb.DMatrix( data, label=label, missing = -999.0)  

> **当需要给样本设置权重时，可以用如下方式**:  
w = np.random.rand(5,1)  
dtrain = xgb.DMatrix( data, label=label, missing = -999.0, weight=w)
## XGBoost实战
XGBoost有两大类接口：XGBoost原生接口 和 scikit-learn接口 ，并且XGBoost能够实现 分类 和 回归 两种任务。
### 基于XGBoost原生接口的分类
import xgboost as xgb 

params = {
    'booster': 'gbtree',
    'objective': 'multi:softmax',
    'num_class': 3,
    'gamma': 0.1,
    'max_depth': 6,
    'lambda': 2,
    'subsample': 0.7,
    'colsample_bytree': 0.7,
    'min_child_weight': 3,
    'silent': 1,
    'eta': 0.1,
    'seed': 1000,
    'nthread': 4,
}

plst = params.items()

**dtrain = xgb.DMatrix(X_train, y_train)**    
num_rounds = 500  
**model = xgb.train(plst, dtrain, num_rounds)**   

#对测试集进行预测  
dtest = xgb.DMatrix(X_test)  
ans = model.predict(dtest)
### 基于XGBoost原生接口的回归  
代码同上，详见参考文档
### 基于Scikit-learn接口的分类  
import xgboost as xgb 

#训练模型  
**model = xgb.XGBClassifier**(max_depth=5, learning_rate=0.1, n_estimators=160, silent=True, **objective='multi:softmax'**) //多分类   
**model.fit(X_train, y_train)**
### 基于Scikit-learn接口的回归
**model = xgb.XGBRegressor**(max_depth=5, learning_rate=0.1, n_estimators=160, silent=True, **objective='reg:gamma')**  
**model.fit(X_train, y_train)**  
## 保存、加载模型
> 在训练完成之后可以将模型保存下来，也可以查看模型内部的结构  
bst.save_model('test.model')

> 导出模型和特征映射（Map）:你可以导出模型到txt文件并浏览模型的含义  
#dump model  
bst.dump_model('dump.raw.txt')  
#dump model with feature map  
bst.dump_model('dump.raw.txt','featmap.txt')

> 通过如下方式可以加载模型：   
bst = xgb.Booster({'nthread':4}) #init model  
bst.load_model("model.bin")      #load data


## reference
[史上最详细的XGBoost实战](https://zhuanlan.zhihu.com/p/31182879)  
