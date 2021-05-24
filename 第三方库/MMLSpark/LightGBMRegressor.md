## 一、mmlspark.lightgbm.LightGBMRegressor
classmmlspark.lightgbm.LightGBMRegressor.LightGBMRegressor(alpha=0.9, baggingFraction=1.0, baggingFreq=0, baggingSeed=3, boostFromAverage=True, boostingType='gbdt', categoricalSlotIndexes=None, categoricalSlotNames=None, defaultListenPort=12400, earlyStoppingRound=0, featureFraction=1.0, featuresCol='features', initScoreCol=None, isProvideTrainingMetric=False, labelCol='label', lambdaL1=0.0, lambdaL2=0.0, learningRate=0.1, maxBin=255, maxDepth=-1, metric='', minSumHessianInLeaf=0.001, modelString='', numBatches=0, numIterations=100, numLeaves=31, objective='regression', parallelism='data_parallel', predictionCol='prediction', timeout=1200.0, tweedieVariancePower=1.5, useBarrierExecutionMode=False, validationIndicatorCol=None, verbosity=1, weightCol=None)

### Choose Appropriate Parallel Algorithm
LightGBM provides 3 distributed learning algorithms now.
<table class="docutils align-default">
<colgroup>
<col style="width: 43%" />
<col style="width: 57%" />
</colgroup>
<thead>
<tr class="row-odd"><th class="head"><p>Parallel Algorithm</p></th>
<th class="head"><p>How to Use</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p>Data parallel</p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">tree_learner=data</span></code></p></td>
</tr>
<tr class="row-odd"><td><p>Feature parallel</p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">tree_learner=feature</span></code></p></td>
</tr>
<tr class="row-even"><td><p>Voting parallel</p></td>
<td><p><code class="docutils literal notranslate"><span class="pre">tree_learner=voting</span></code></p></td>
</tr>
</tbody>
</table>

These algorithms are suited for different scenarios, which is listed in the following table:
<table class="docutils align-default">
<colgroup>
<col style="width: 41%" />
<col style="width: 31%" />
<col style="width: 28%" />
</colgroup>
<thead>
<tr class="row-odd"><th class="head"></th>
<th class="head"><p>#data is small</p></th>
<th class="head"><p>#data is large</p></th>
</tr>
</thead>
<tbody>
<tr class="row-even"><td><p><strong>#feature is small</strong></p></td>
<td><p>Feature Parallel</p></td>
<td><p>Data Parallel</p></td>
</tr>
<tr class="row-odd"><td><p><strong>#feature is large</strong></p></td>
<td><p>Feature Parallel</p></td>
<td><p>Voting Parallel</p></td>
</tr>
</tbody>
</table>

&nbsp;
## 二、示例
In this example, we use LightGBM to build a classification model in order to predict bankruptcy.

Read dataset:
```
df = spark.read.format("csv")\
  .option("header", True)\
  .option("inferSchema", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/company_bankruptcy_prediction_data.csv")
# print dataset size
print("records read: " + str(df.count()))
print("Schema: ")
df.printSchema()
```
Split the dataset into train and test:
```
train, test = df.randomSplit([0.85, 0.15], seed=1)
```
Add featurizer to convert features to vector:
```
from pyspark.ml.feature import VectorAssembler
feature_cols = df.columns[1:]
featurizer = VectorAssembler(
    inputCols=feature_cols,
    outputCol='features'
)
train_data = featurizer.transform(train)['Bankrupt?', 'features']
test_data = featurizer.transform(test)['Bankrupt?', 'features']
```
Check if the data is unbalanced:
```
display(train_data.groupBy("Bankrupt?").count())
```
Model Training:
```
from mmlspark.lightgbm import LightGBMClassifier
model = LightGBMClassifier(objective="binary", featuresCol="features", labelCol="Bankrupt?", isUnbalance=True)
```
```
model = model.fit(train_data)
```
By calling "saveNativeModel", it allows you to extract the underlying lightGBM model for fast deployment after you train on Spark.
```
from mmlspark.lightgbm import LightGBMClassificationModel
model.saveNativeModel("/lgbmclassifier.model")
model = LightGBMClassificationModel.loadNativeModelFromFile("/lgbmclassifier.model")
```
Feature Importances Visualization:
```
import pandas as pd
import matplotlib.pyplot as plt

feature_importances = model.getFeatureImportances()
fi = pd.Series(feature_importances,index = feature_cols)
fi = fi.sort_values(ascending = True)
f_index = fi.index
f_values = fi.values
 
# print feature importances 
print ('f_index:',f_index)
print ('f_values:',f_values)

# plot
x_index = list(range(len(fi)))
x_index = [x/len(fi) for x in x_index]
plt.rcParams['figure.figsize'] = (20,20)
plt.barh(x_index,f_values,height = 0.028 ,align="center",color = 'tan',tick_label=f_index)
plt.xlabel('importances')
plt.ylabel('features')
plt.show()
```
Model Prediction:
```
predictions = model.transform(test_data)
predictions.limit(10).toPandas()
```
```
from mmlspark.train import ComputeModelStatistics
metrics = ComputeModelStatistics(evaluationMetric="classification", labelCol='Bankrupt?', scoredLabelsCol='prediction').transform(predictions)
display(metrics)
```

&nbsp;
## References
[LightGBM - Overview.ipynb](https://github.com/Azure/mmlspark/blob/master/notebooks/samples/LightGBM%20-%20Overview.ipynb)   
[mmlspark.lightgbm.LightGBMRegressor module](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.lightgbm.html#module-mmlspark.lightgbm.LightGBMRegressor)   
[mmlspark package](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc3/pyspark/mmlspark.html#module-mmlspark)    
[Distributed Learning Guide](https://lightgbm.readthedocs.io/en/latest/Parallel-Learning-Guide.html)
