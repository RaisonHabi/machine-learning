## mmlspark.lightgbm.LightGBMRegressor
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
## References
[mmlspark.lightgbm.LightGBMRegressor module](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.lightgbm.html#module-mmlspark.lightgbm.LightGBMRegressor)   
[mmlspark package](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc3/pyspark/mmlspark.html#module-mmlspark)    
[Distributed Learning Guide](https://lightgbm.readthedocs.io/en/latest/Parallel-Learning-Guide.html)
