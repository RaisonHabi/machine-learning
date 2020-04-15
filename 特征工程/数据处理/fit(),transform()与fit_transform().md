## Fit()
Method calculates the parameters μ and σ and saves them as internal objects.  
解释：简单来说，就是求得训练集X的均值啊，方差啊，最大值啊，最小值啊这些训练集X固有的属性。可以理解为一个训练过程  
## Transform()
Method using these calculated parameters apply the transformation to a particular dataset.  
解释：在Fit的基础上，进行标准化，降维，归一化等操作（看具体用的是哪个工具，如PCA，StandardScaler等）。  

## Fit_transform()
joins the fit() and transform() method for transformation of dataset.  
解释：fit_transform是fit和transform的组合，既包括了训练又包含了转换。  
> fit_transform(trainData)对部分数据先拟合fit，找到该part的整体指标，如均值、方差、最大值最小值等等（根据具体转换的目的），然后对该trainData进行转换transform，从而实现数据的标准化、归一化等等。


## transform()和fit_transform()
二者的功能都是对数据进行某种统一处理（比如标准化~N(0,1)，将数据缩放(映射)到某个固定区间，归一化，正则化等）  

**根据对之前部分trainData进行fit的整体指标，对剩余的数据（testData）使用同样的均值、方差、最大最小值等指标进行转换transform(testData)，从而保证train、test处理方式相同。所以，一般都是这么用：**  
示例：  
> from sklearn.preprocessing import StandardScaler  
sc = StandardScaler()  
*sc.fit_tranform*(***X_train***) #等同于sc.fit(X_train) sc.transform(X_train)   
*sc.tranform*(***X_test***)  

**Note:**  
如果fit_transfrom(trainData)后，**使用fit_transform(testData)而不transform(testData)**，虽然也能归一化，但是两个结果***不是在同一个“标准”下的***，具有明显差异。
## ferefence
[sklearn数据预处理中fit(),transform()与fit_transform()的区别](https://zhuanlan.zhihu.com/p/42297868)
