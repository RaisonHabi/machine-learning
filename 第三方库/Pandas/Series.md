## Series 
pandas两大数据结构中（DataFrame，Series）的一种  
**Series的定义**  
Series是一种类似于一维数组的对象，它由一组数据（各种NumPy数据类型）以及一组与之相关的数据标签（即索引）组成  
***Series对象本质上是一个NumPy的数组，因此NumPy的数组处理函数可以直接对Series进行处理***  
但是Series除了可以使用位置作为下标存取元素之外，还可以使用标签下标存取元素，这一点和字典相似  
每个Series对象实际上都由两个数组组成：  
> index: 它是从NumPy数组继承的Index对象，保存标签信息  
values: 保存值的NumPy数组。

**注意点**  
>  1. Series是一种类似于一维数组（数组：ndarray）的对象  
 2. 它的数据类型没有限制（各种NumPy数据类型）  
 3. 它有索引，把索引当做数据的标签（key）看待，这样就类似字典了（只是类似，实质上是数组）  
 4.Series同时具有数组和字典的功能，因此它也支持一些字典的方法  

示例：  
创建Series:  
> arr=[1,2,3,4] #创建数组  
series_1=Series(arr)  
或  
series_2=Series([1,2,3,4])  

创建包含多种数据类型的Series：  
> series_3=Series([1,2,'3',4,'a'])

## Series索引  
Series创建后会自动生成索引，默认从0开始  
可以指定和修改索引:  
> series_4.index=['a','b','c']  
修改索引除了这里的直接修改还有一个reindex()方法

## Series增删改查
Series创建后可以对数据进行增删改查  
**增：**  
Series的add()方法是加法计算**不是**增加Series元素用的  
**使用append连接其他Series**  
**删：drop()**  
**改：**  
series_4['a']=4  
**查：**  
通过索引查单值  
series_4['a']  
## 通过字典创建Series
series_5=Series({'a':1,'b':2,'c':3})  
a    1  
b    2  
c    3  


## reference 
[【pandas】[1] Series 入门(创建和增删改查)](https://blog.csdn.net/zutsoft/article/details/51482573)

