## 背景
LIBSVM 是台湾大学林智仁(Chih-Jen Lin)博士等开发设计的一个操作简单、易于使用、快速有效的通用**SVM软件包**。  
可以解决分类问题（包括C- SVC、n - SVC ）、回归问题（包括e - SVR、n - SVR ）以及分布估计（one-class-SVM ）等问题，提供了线性、多项式、径向基和S形函数四种常用的核函数供选择（也可以自定义核函数），可以有效地解决多类问题、交叉验证选择参数、对不平衡样本加权、多类问题的概率估计等。

由于libsvm程序小，运用灵活，输入参数较少，并且是开源的，易于扩展，目前已经成为国内应用最多的SVM库。

解压后可以看到，主要有6个文件夹和一些c++源码文件：  
> Java——主要是应用于java平台；  
matlab——应用于matlab平台；  
Python——是用来参数优选的工具，稍后介绍；  
svm-toy——可视化的工具，用来展示训练数据和分类界面，里面是源码，其编译后的程序在windows文件夹下；  
tools——主要包含四个python文件，用来数据集抽样(subset)，参数优选（grid），集成测试(easy)，数据检查(checkdata)；  
windows——包含libSVM四个exe程序包，我们所用的库就是他们。  

其他.h和.cpp文件都是程序的源码，可以编译出相应的.exe文件。其中，最重要的是svm.h和svm.cpp文件，svm-predict.c、svm-scale.c和svm-train.c（还有一个svm-toy.c在svm-toy文件夹中）都是调用的这个文件中的接口函数，编译后就是windows下相应的四个exe程序。还有个heart_scale，是一个样本文件，可以用记事本打开，用来测试用的。另外，里面的 README 跟 FAQ也是很好的文件，可以看英文试试不算太难。

## 数据格式
虽然LIBSVM已经实现了最简单的使用方式：输入数据即可得出结果。但是，这里输入数据时得符合一定格式，这样LIBSVM才能去正确的运行。  
LIBSVM训练文件和测试文件的的格式要求如下：  
> <label> <index1>:<value1> <index2>:<value2>...//实际使用时无尖括号<>  
<类别> <索引1>:<属性值1> <索引2>:<属性值2>...//实际使用时无尖括号<>  

注意：  
> <类别>一般表示正样本或负样本，也就是整数1或-1;  
索引1、索引2是**从1开始的正整数**，也就是维数;  
属性值1、属性值2表示样本的属性;

比如，有一个男生一个女生，假设男生为1、女生为-1。有两个属性体重和身高，则格式如下：  
+1 1:70 2:178  
-1 1:50 2:165

## reference
[LIBSVM入门解读](https://blog.csdn.net/taohuaxinmu123/article/details/20370525)  
[libsvm的使用方法（python）](https://www.jianshu.com/p/e9cd040de6ce)  
[libsvm/libffm与dataframe格式相互转换](https://blog.csdn.net/olizxq/article/details/89680357)
