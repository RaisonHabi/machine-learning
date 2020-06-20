## 安装
### 1.手动安装
#### 下载源码，编译lib_lightgbm.so （对于windows 则是 lib_lightgbm.dll ）
```
git clone --recursive https://github.com/Microsoft/LightGBM
# 对于 windows 用户，需要执行：
#   git submodule init
#   git submodule update
cd LightGBM
mkdir build
cd build
cmake ..
make -j4
```
如果需要支持GPU，则执行cmake 时指定：
```
cmake -DUSE_GPU=1 ..

# 如果你已经安装了 NVIDIA OpenGL，则使用：
# cmake -DUSE_GPU=1 -DOpenCL_LIBRARY=/usr/local/cuda/lib64/libOpenCL.so -DOpenCL_INCLUDE_DIR=/usr/local/cuda/include/ ..
```
如果想要protobuf 来保存和加载模型，则先安装protobuf c++版本，然后使用：
```
cmake -DUSE_PROTO=ON ..
```
当前版本不支持该功能

#### 安装python 支持
其中 --precompile 指示：已经编译过，不需要再次编译
```
cd python-package
sudo python setup.py install --precompile
```
### 2.直接pip 安装
```
pip install lightgbm
pip install --no-binary :all: lightgbm #从源码编译安装
pip install lightgbm --install-option=--mpi #从源码编译安装 MPI 版本
pip install lightgbm --install-option=--gpu #从源码编译安装 GPU 版本
pip install lightgbm --install-option=--gpu --install-option="--opencl-include-dir=/usr/local/cuda/include/" --install-option="--opencl-library=/usr/local/cuda/lib64/libOpenCL.so" #从源码编译安装，指定配置
#可选的配置有：
#    boost-root
#    boost-dir
#    boost-include-dir
#    boost-librarydir
#    opencl-include-dir
#    opencl-library
```

&nbsp;
## 模型接口
### Booster
1.LightGBM 中的 Booster类：
```
class lightgbm.Booster(params=None, train_set=None, model_file=None, silent=False)
```
参数：
```
params： 一个字典或者None，给出了Booster 的参数。默认为None
train_set： 一个Dataset对象或者None，给出了训练集。 默认为None
model_file： 一个字符串或者None，给出了model file 的路径。 默认为None
silent： 一个布尔值，指示是否在构建过程中打印消息。默认为False
```

<details>
<summary>2.方法：展开查看</summary>
<pre><code>
.add_valid(data,name)： 添加一个验证集。

参数：

data： 一个Dataset，代表一个验证集
name： 一个字符串，表示该验证集的名字。不同的验证集通过名字来区分
.attr(key)： 获取属性的值。

参数：key： 一个字符串，表示属性的名字
返回值：该属性的名字。如果属性不存在则返回None
.current_iteration()： 返回当前的迭代的index（即迭代的编号）

.dump_model(num_iteration=-1)： dump 当前的Booster 对象为json 格式。

参数：num_iteration： 一个整数，指定需要dump 第几轮训练的结果。如果小于0，则最佳迭代步的结果（如果存在的话）将被dump。默认为-1。
返回值：一个字典，它表示dump 之后的json 
.eval(data,name,feval=None)： 对指定的数据集evaluate

参数：

data： 一个Dataset 对象，代表被评估的数据集
name： 一个字符串，表示被评估的数据集的名字。不同的验证集通过名字来区分
feval： 一个可调用对象或者None， 它表示自定义的evaluation 函数。默认为None。它的输入为(y_true, y_pred)、或者( y_true, y_pred, weight) 、或者(y_true, y_pred, weight, group)， 返回一个元组：(eval_name,eval_result,is_higher_better) 。或者返回该元组的列表。
返回值：一个列表，给出了evaluation 的结果。

.eval_train(feval=None)： 对训练集进行evaluate

参数：feval： 参考.eval() 方法
返回值：一个列表，给出了evaluation 的结果。
.eval_valid(feval=None)：对验证集进行evaluate

参数：feval： 参考.eval() 方法
返回值：一个列表，给出了evaluation 的结果。
.feature_importance(importance_type='split', iteration=-1)： 获取特征的importance

参数：

importance_type： 一个字符串，给出了特征的importance衡量指标。默认为'split'。 可以为：

'split'： 此时特征重要性衡量标准为：该特征在所有的树中，被用于划分数据集的总次数。
'gain'： 此时特征重要性衡量标准为：该特征在所有的树中获取的总收益。
iteration： 一个整数，指定需要考虑的是第几轮训练的结果。如果小于0，则最佳迭代步的结果（如果存在的话）将被考虑。默认为-1。

返回值：一个numpy array，表示每个特征的重要性

.feature_name()： 获取每个特征的名字。

返回值：一个字符串的列表，表示每个特征的名字
.free_dataset()：释放Booster 对象的数据集

.free_network()： 释放Booster 对象的Network

.get_leaf_output(tree_id, leaf_id)： 获取指定叶子的输出

输入：

tree_id： 一个整数，表示子学习器的编号
leaf_id： 一个整数，表示该子学习器的叶子的编号
返回值：一个浮点数，表示该叶子节点的输出

.num_feature()： 获取特征的数量（即由多少列特征）

.predict(data, num_iteration=-1, raw_score=False, pred_leaf=False, pred_contrib=False, data_has_header=False, is_reshape=True, pred_parameter=None)： 执行预测

参数：

data： 一个字符串、numpy array 或者scipy.sparse， 表示被测试的数据集。如果为字符串，则表示测试集所在的文件的文件名。

注意：如果是numpy array 或者 pandas dataframe 时，要求数据的列必须与训练时的列顺序一致。

num_iteration：一个整数，表示用第几轮的迭代结果来预测。如果小于0，则最佳迭代步的结果（如果存在的话）将被使用。默认为-1。

raw_score： 一个布尔值，指示是否输出raw scores。 默认为False

pred_leaf： 一个布尔值。如果为True，则会输出每个样本在每个子树的哪个叶子上。它是一个nsample x ntrees 的矩阵。默认为False。

每个子树的叶节点都是从1 开始编号的。

pred_contrib：一个布尔值。如果为True， 则输出每个特征对每个样本预测结果的贡献程度。它是一个nsample x ( nfeature+1) 的矩阵。默认为False。

之所以加1，是因为有bias 的因素。它位于最后一列。

其中样本所有的贡献程度相加，就是该样本最终的预测的结果。

data_has_header：一个布尔值，指示数据集是否含有标题。仅当data 是字符串时有效。默认为False。

is_reshape：一个布尔值，指示是否reshape 结果成[nrow,ncol]。 默认为True

pred_parameter： 一个字典或者None，给出了其它的参数。默认为None

返回值：一个numpy array，表示预测结果

.reset_parameter(params)：重设Booster 的参数。

参数：params：一个字典，给出了新的参数
.rollback_one_iter()： 将Booster 回滚一个迭代步

.save_model(filename,num_iteration=-1)： 保存Booster 对象到文件中。

参数：

filename： 一个字符串，给出了保存的文件的文件名
num_iteration： 一个整数，指定需要保存的是第几轮训练的结果。如果小于0，则最佳迭代步的结果（如果存在的话）将被保存。默认为-1。
.set_attr(**kwargs)： 设置Booster 的属性。

参数：kwargs： 关键字参数，用于设定Booster 属性。对于值为None 的设置，等效于删除该属性。
.set_network(machines,local_listen_port=12400,listen_time_out=120,num_machines=1)： 配置网络

参数：

machines：一个字符串的列表、或者字符串的集合。它给出了每台机器的名字
local_listen_port： 一个整数，默认为 12400，指定了监听端口
listen_time_out： 一个整数，默认为120， 制定了socket 超时的时间（单位为分钟）
num_machines： 一个整数，默认为1，表示并行学习的机器的数量
.set_train_data_name(name)： 设置训练集的名字

参数：name： 一个字符串，表示训练集的名字
.update(train_set=None, fobj=None)： 更新一个迭代步

参数：

train_set： 一个Dataset 或者None， 表示训练集。如果为None，则上一个训练集被使用

fobj： 一个可调用对象或者None，表示自定义的目标函数。

注意：如果是多类别分类任务，则：score 首先根据class_id 进行分组，然后根据row_id 分组。如果你想得到第i 个样本在第j 个类别上的得分，访问方式为：score[j*num_data+i]。 同理：grad 和 hess 也是以这样的方式访问。

返回值：一个布尔值，指示该次更新迭代步是否成功结束。
</code></pre>
</details>

3.示例：
```
_label_map={
  # 'Iris-setosa':0,
  'Iris-versicolor':0,
  'Iris-virginica':1
}
class BoosterTest:
  def __init__(self):
    df = pd.read_csv('./data/iris.csv')
    _feature_names = ['Sepal Length', 'Sepal Width', 'Petal Length', 'Petal Width']
    x = df[_feature_names]
    y = df['Class'].map(lambda x: _label_map[x])
​
    train_X, test_X, train_Y, test_Y = train_test_split(x, y, test_size=0.3,
                   stratify=y, shuffle=True, random_state=1)
    print([item.shape for item in (train_X, test_X, train_Y, test_Y)])
    self._train_set = lgb.Dataset(data=train_X, label=train_Y, 
                                  feature_name=_feature_names)
    self._validate_set = lgb.Dataset(data=test_X, label=test_Y, 
                                  reference=self._train_set)
    self._booster = lgb.Booster(params={
      'boosting': 'gbdt',
      'verbosity': 1,  # 打印消息
      'learning_rate': 0.1,  # 学习率
      'num_leaves':5,
      'max_depth': 5,
      'objective': 'binary',
      'metric': 'auc',
      'seed': 321,
    },
      train_set=self._train_set)
    self._booster.add_valid(self._validate_set,'validate1')
    self._booster.set_train_data_name('trainAAAAA')
​
  def print_attr(self):
    print('feature name:',self._booster.feature_name())
    # feature name: ['Sepal_Length', 'Sepal_Width', 'Petal_Length', 'Petal_Width']
    print('feature nums:', self._booster.num_feature())
    # feature nums: 4
  def test_train(self):
    for i in range(0,4):
      self._booster.update(self._train_set)
      print('after iter:%d'%self._booster.current_iteration())
      print('train eval:',self._booster.eval(self._train_set, name='train'))
      print('test eval:',self._booster.eval(self._validate_set,name='eval'))
   # after iter:1
   # train eval: [('train', 'auc', 0.9776530612244898, True)]
   # test eval: [('eval', 'auc', 0.9783333333333334, True)]
   # after iter:2
   # train eval: [('train', 'auc', 0.9907142857142858, True)]
   # test eval: [('eval', 'auc', 0.9872222222222222, True)]
   # after iter:3
   # train eval: [('train', 'auc', 0.9922448979591837, True)]
   # test eval: [('eval', 'auc', 0.9888888888888889, True)]
   # after iter:4
   # train eval: [('train', 'auc', 0.9922448979591837, True)]
   # test eval: [('eval', 'auc', 0.9888888888888889, True)]
  def test(self):
    self.print_attr()
    self.test_train()
```

### 直接学习
1. lightgbm.train() 函数执行直接训练。
```
lightgbm.train(params, train_set, num_boost_round=100, valid_sets=None,
  valid_names=None, fobj=None, feval=None, init_model=None, feature_name='auto',
  categorical_feature='auto', early_stopping_rounds=None, evals_result=None, 
  verbose_eval=True, learning_rates=None, keep_training_booster=False, callbacks=None)
```
参数：
```
params： 一个字典，给出了训练参数
train_set： 一个Dataset对象，给出了训练集
num_boost_round： 一个整数，给出了boosting iteration 的次数。默认为100
valid_sets：一个Dataset 的列表或者None，给出了训练期间用于evaluate的数据集。默认为None
```
2. lightgbm.cv() 函数执行交叉验证训练。
```
lightgbm.cv(params, train_set, num_boost_round=10, folds=None, nfold=5,
    stratified=True, shuffle=True, metrics=None, fobj=None, feval=None,
    init_model=None, feature_name='auto', categorical_feature='auto', 
    early_stopping_rounds=None, fpreproc=None, verbose_eval=None, show_stdv=True,
    seed=0, callbacks=None)
```
参数：
```
params： 一个字典，给出了训练参数
train_set： 一个Dataset对象，给出了训练集
num_boost_round：一个整数，给出了boosting iteration 的次数。默认为10
folds：一个生成器、一个迭代器、或者None。 如果是生成器或者迭代器，则其迭代结果为元组：(训练部分样本下标列表,测试部分样本下标列表)，分别给出了每个fold 的训练部分和测试部分的下标。默认为None。
该参数比其它的拆分参数优先级更高。
nfold：一个整数，指定了CV 的数量。默认为5 。
```
### scikit-learn API
#### LGBMModel
LGBMModel 实现了lightgbm 类似于scikit-learn 的接口
```
class lightgbm.LGBMModel(boosting_type='gbdt', num_leaves=31, max_depth=-1,
   learning_rate=0.1, n_estimators=10, max_bin=255, subsample_for_bin=200000,
   objective=None, min_split_gain=0.0, min_child_weight=0.001, min_child_samples=20,
   subsample=1.0, subsample_freq=1, colsample_bytree=1.0, reg_alpha=0.0,
   reg_lambda=0.0, random_state=None, n_jobs=-1, silent=True,class_weight=None,
   **kwargs)
```
1.参数：
```
boosting_type： 一个字符串，指定了基学习器的算法。默认为'gbdt'。 可以为：
'gbdt'： 表示传统的梯度提升决策树。默认值为'gbdt'
'rf'： 表示随机森林。
'dart'： 表示带dropout 的gbdt
goss：表示Gradient-based One-Side Sampling 的gbdt
num_leaves：一个整数，给出了一棵树上的叶子数。默认为 31

max_depth：一个整数，限制了树模型的最大深度，默认值为-1。
如果小于0，则表示没有限制。

learning_rate： 一个浮点数，给出了学习率。默认为 0.1

n_estimators：一个整数，给出了boosted trees 的数量。默认为 10

max_bin： 一个整数， 指定每个特征的最大分桶数量。默认为255。

class_weight：给出了每个类别的权重占比。
可以为字符串'balanced'，此时类别权重反比与类别的频率。
可以为字典，此时人工给出了每个类别的权重。
如果为None，则认为每个类别的比例一样。
该参数仅用于多类分类问题。对于二类分类问题，可以使用is_unbalance 参数。

subsample_for_bin：一个整数，表示用来构建直方图的样本的数量。默认为200000。

objective： 一个字符串、可调用对象或者为None，表示问题类型以及对应的目标函数。参考2.2.1 核心参数->objective 。
默认为None ，此时对于LGBMRegressor 为'regression'；对于LGBMClassifier 为'binary' 或者'multiclass'；对于LGBMRanker 为'lambdarank'。
如果为自定义的可调用对象，则它的签名为：objective(y_true, y_pred) -> grad, hess ；或者签名为：objective(y_true, y_pred, group) -> grad, hess。其中：
y_true： 一个形状为(n_samples,)（对于多类分类问题，则是(n_samples,n_classes)） 的array-like 对象，给出了真实的标签值。
y_pred： 一个形状为(n_samples,) （对于多类分类问题，则是(n_samples,n_classes)）的array-like 对象，给出了预测的标签值。
group： 一个array-like对象，给出了数据的分组信息。它用于ranking 任务
grad：一个形状为(n_samples,)（对于多类分类问题，则是(n_samples,n_classes)） 的array-like 对象，给出了每个样本的梯度值。
hess：一个形状为(n_samples,)（对于多类分类问题，则是(n_samples,n_classes)） 的array-like 对象，给出了每个样本的二阶导数值。

min_split_gain：一个浮点数，表示执行切分的最小增益，默认为0

min_child_weight：一个浮点数，表示一个叶子节点上的最小hessian 之和。（也就是叶节点样本权重之和的最小值） 默认为1e-3 。

min_child_samples： 一个整数，表示一个叶子节点上包含的最少样本数量。默认值为 20

subsample： 一个浮点数，表示训练样本的采样比例。参考2.2.2 学习控制参数->subsample 。

subsample_freq：一个浮点数，表示训练样本的采样频率。参考2.2.2 学习控制参数->subsample_freq 。

colsample_bytree：一个浮点数，表示特征的采样比例。参考2.2.2 学习控制参数->colsample_bytree 。

reg_alpha： 一个浮点数，表示L1正则化系数。默认为0

reg_lambda：一个浮点数，表示L2正则化系数。默认为0

random_state：一个整数或者None，表示随机数种子。如果为None，则使用默认的种子。默认为None

n_jobs：一个整数，指定并行的线程数量。如果为-1，则表示使用所有的CPU。默认为-1

silent：一个布尔值，指示是否在训练过程中屏蔽输出。默认为True。

kwargs：其它的参数。
```
2.属性：
```
.n_features_：一个整数，给出了特征的数量
.classes_：一个形状为(n_classes,) 的numpy array， 给出了样本的标签。（仅仅在分类问题中有效）
.n_classes_：一个整数，给出了类别的数量。（仅仅在分类问题中有效）
.best_score_：一个字典或者None，给出了训练完毕模型的最好的score
.best_iteration_：一个字典或者None。当early_stopping_round 参数设定时，它给出了训练完毕模型的最好的迭代步。
.objective_：一个字符串或者可调用对象，给出了训练模型的目标函数
.booster_：一个Booster对象，给出了底层的Booster 对象。
.evals_result_：一个字典或者None。当early_stopping_round 参数设定时，它给出了模型的evaluation results 。
.feature_importances_： 一个形状为(n_features,) 的 numpy array，给出了特征的重要性（值越大，则对于的特征越重要）。
```
#### LGBMClassifier
LGBMClassifier 是LGBMModel 的子类，它用于分类任务。
```
class lightgbm.LGBMClassifier(boosting_type='gbdt', num_leaves=31, max_depth=-1,
   learning_rate=0.1, n_estimators=10, max_bin=255, subsample_for_bin=200000,
   objective=None, min_split_gain=0.0, min_child_weight=0.001, min_child_samples=20, 
   subsample=1.0, subsample_freq=1, colsample_bytree=1.0, reg_alpha=0.0,
   reg_lambda=0.0, random_state=None, n_jobs=-1, silent=True, **kwargs)
  ```
1.参数：参考LGBMModel

2.属性：参考LGBMModel
3.方法：
```
.fit()： 训练模型
fit(X, y, sample_weight=None, init_score=None, eval_set=None, eval_names=None,
    eval_sample_weight=None, eval_init_score=None, eval_metric='logloss',
    early_stopping_rounds=None, verbose=True, feature_name='auto',
    categorical_feature='auto', callbacks=None)
参数：参考LGBMModel.fit()
返回值：参考LGBMModel.fit()

.predict_proba(X, raw_score=False, num_iteration=0)：预测每个样本在每个类上的概率。
参数：参考LGBMModel.predict()
返回值：一个形状为(n_samples,n_classes) 的array-like 对象，给出了每个样本在每个类别上的概率。

其它方法参考LGBMModel
```

&nbsp;
## LGBMClassifier参数
```
1.boosting_type=‘gbdt’# 提升树的类型 gbdt,dart,goss,rf
2.num_leavel=32#树的最大叶子数，对比xgboost一般为2^(max_depth)
3.max_depth=-1#最大树的深度
4.learning_rate#学习率
5.n_estimators=10: 拟合的树的棵树，相当于训练轮数
6.subsample=1.0: 训练样本采样率 行
7.colsample_bytree=1.0: 训练特征采样率 列
8.subsample_freq=1: 子样本频率
9.reg_alpha=0.0: L1正则化系数
10.reg_lambda=0.0: L2正则化系数
11.random_state=None: 随机种子数
12.n_jobs=-1: 并行运行多线程核心数
13.silent=True: 训练过程是否打印日志信息
14.min_split_gain=0.0: 最小分割增益
15.min_child_weight=0.001: 分支结点的最小权重
```
[LGBMClassifier参数](https://blog.csdn.net/starmoth/article/details/84586709)

&nbsp;
## reference
[LightGBM使用](http://www.huaxiaozhuan.com/%E5%B7%A5%E5%85%B7/lightgbm/chapters/lightgbm_usage.html)
