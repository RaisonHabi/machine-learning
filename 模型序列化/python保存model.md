## Serialize Your XGBoost Model with Pickle
Pickle is the standard way of serializing objects in Python.  
示例：  
> import pickle  
#save model to file  
pickle.dump(model, open("pima.pickle.dat", "wb"))  
#load model from file  
loaded_model = pickle.load(open("pima.pickle.dat", "rb"))  

详见参考

## reference
[How to Save Gradient Boosting Models with XGBoost in Python](https://machinelearningmastery.com/save-gradient-boosting-models-xgboost-python/)
