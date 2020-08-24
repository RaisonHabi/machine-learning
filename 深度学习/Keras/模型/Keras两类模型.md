##  一、Keras 中有两类主要的模型
Keras 的核心数据结构是 model，一种组织网络层的方式。  
最简单的模型是 Sequential 顺序模型，它由多个网络层线性堆叠。  
对于更复杂的结构，你应该使用 Keras 函数式 API，它允许构建任意的神经网络图。
> Sequential 顺序模型   
使用函数式 API 的 Model 类模型

快速入门文档详见下方链接：  
只需一行代码就能评估模型性能：  
**loss_and_metrics = model.evaluate(x_test, y_test, batch_size=128)**  
[Keras: 基于 Python 的深度学习库](https://keras.io/zh/)

### 1、Keras Sequential 顺序模型
[开始使用 Keras Sequential 顺序模型](https://keras.io/zh/getting-started/sequential-model-guide/)  
顺序模型是多个网络层的线性堆叠。


### 2、Keras 函数式 API
[开始使用 Keras 函数式 API](https://keras.io/zh/getting-started/functional-api-guide/)    
[The Functional API](https://keras.io/guides/functional_api/)  
Keras 函数式 API 是定义复杂模型（如多输出模型、有向无环图，或具有共享层的模型）的方法。

### 3、three ways to create Keras models
There are three ways to create Keras models:
```
The Sequential model, which is very straightforward (a simple list of layers), 
but is limited to single-input, single-output stacks of layers (as the name gives away).

The Functional API, which is an easy-to-use, fully-featured API that supports arbitrary model architectures.
For most people and most use cases, this is what you should be using. This is the Keras "industry strength" model.

Model subclassing, where you implement everything from scratch on your own. 
Use this if you have complex, out-of-the-box research use cases.
```
[Models API](https://keras.io/api/models/)

&nbsp;
## 二、这些模型有许多共同的方法和属性
> model.layers 是包含模型网络层的展平列表。  
model.inputs 是模型输入张量的列表。  
model.outputs 是模型输出张量的列表。  
**model.summary() 打印出模型概述信息。** 它是 utils.print_summary 的简捷调用。  
model.get_config() 返回包含模型配置信息的字典。  
>> 通过以下代码，就可以**根据这些配置信息重新实例化模型**：  
config = model.get_config()  
model = Model.from_config(config)   
#或者，对于 Sequential:  
model = Sequential.from_config(config)  

> model.get_weights() 返回模型中所有权重张量的列表，类型为 Numpy 数组。  
model.set_weights(weights) 从 Numpy 数组中为模型设置权重。***列表中的数组必须与 get_weights() 返回的权重具有相同的尺寸***。  
model.to_json() 以 JSON 字符串的形式返回模型的表示。***请注意，该表示不包括权重，仅包含结构***。  
可以通过以下方式**从 JSON 字符串重新实例化同一模型（使用重新初始化的权重）**：  
>> from keras.models import model_from_json  
json_string = model.to_json()  
model = model_from_json(json_string)  

> model.to_yaml() 以 YAML 字符串的形式返回模型的表示。***请注意，该表示不包括权重，只包含结构***。  
可以通过以下代码，**从 YAML 字符串中重新实例化相同的模型（使用重新初始化的权重）**：  
>> from keras.models import model_from_yaml  
yaml_string = model.to_yaml()  
model = model_from_yaml(yaml_string)  

> model.save_weights(filepath) 将模型权重存储为 HDF5 文件。  
model.load_weights(filepath, by_name=False): 从 HDF5 文件（由 save_weights 创建）中加载权重。  
默认情况下，模型的结构应该是不变的。   
**如果想将权重载入不同的模型（部分层相同）， 设置 by_name=True 来载入那些名字相同的层的权重**。

### YAML
YAML（/ˈjæməl/，尾音类似camel骆驼）是一个可读性高，用来表达数据序列化的格式。

## reference
[关于 Keras 模型](https://keras.io/zh/models/about-keras-models/)  
[Keras FAQ: Frequently Asked Keras Questions](https://keras.io/getting-started/faq/#how-can-i-install-hdf5-or-h5py-to-save-my-models-in-keras)  
