## predict()  
当使用predict()方法进行预测时，返回值是数值，表示样本属于每一个类别的概率  
我们可以使用numpy.argmax()方法找到样本以最大概率所属的类别作为样本的预测标签。  
示例：  
> print(model.predict(imgs))		# 打印概率  
print(np.argmax(model.predict(imgs), axis=1))		# 打印最大概率对应的标签  
## predict_classes()  
当使用predict_classes()方法进行预测时，返回的是类别的索引，即该样本所属的类别标签。  
示例：  
> predictions = model.predict_classes(imgs)  
print(predictions)  

[Keras中predict()方法和predict_classes()方法的区别](https://blog.csdn.net/tszupup/article/details/85275111)
