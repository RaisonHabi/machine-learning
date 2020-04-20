在机器学习里，通常来说我们不能将全部用于数据训练模型，否则我们将没有数据集对该模型进行验证，从而评估我们的模型的预测效果。  
为了解决这一问题，有如下常用的方法：
## The Validation Set Approach
第一种是最简单的，也是很容易就想到的。  
我们可以把整个数据集分成两部分，一部分用于训练，一部分用于验证，这也就是我们经常提到的训练集（training set）和测试集（test set）。

**不过，这个简单的方法存在两个弊端:**  
1.最终模型与参数的选取将极大程度依赖于你对训练集和测试集的划分方法。  
> 在不同的划分方法下，test MSE(mean squared error 均方误差)的变动是很大的，而且对应的最优degree也不一样

2.该方法只用了部分数据进行模型的训练  
> 我们都知道，当用于模型训练的数据量越大时，训练出来的模型通常效果会越好。所以训练集和测试集的划分意味着我们无法充分利用我们手头已有的数据，所以得到的模型效果也会受到一定的影响。

基于这样的背景，有人就提出了Cross-Validation方法，也就是交叉验证。


&nbsp;
## Cross-Validation
### LOOCV
LOOCV方法，即（Leave-one-out cross-validation）。  
像Test set approach一样，LOOCV方法也包含将数据集分为训练集和测试集这一步骤。  
但是不同的是，我们现在只用一个数据作为测试集，其他的数据都作为训练集，并将此步骤重复N次（N为数据集的数据数量）。  

LOOCV有很多优点
> 首先它不受测试集合训练集划分方法的影响，因为每一个数据都单独的做过测试集。  
同时，其用了n-1个数据训练模型，也几乎用到了所有的数据，保证了模型的bias更小。

不过LOOCV的缺点也很明显，那就是计算量过于大，是test set approach耗时的n-1倍。  
> 为了解决计算成本太大的弊端，又有人提供了下面的式子，使得LOOCV计算成本和只训练一个模型一样快。(详见参考文档)

### K-fold Cross Validation
另外一种折中的办法叫做K折交叉验证  
和LOOCV的不同在于，我们每次的测试集将不再只包含一个数据，而是多个，具体数目将根据K的选取决定。  
比如，如果K=5，那么我们利用五折交叉验证的步骤就是：  
> 1.将所有数据集分成5份  
2.不重复地每次取其中一份做测试集，用其他四份做训练集训练模型，之后计算该模型在测试集上的[公式]  
3.将5次的[公式]取平均得到最后的MSE

不难理解，其实LOOCV是一种特殊的K-fold Cross Validation（K=N）。  
**事实上LOOCV和10-fold CV对test MSE的估计是很相似的，但是相比LOOCV，10-fold CV的计算成本却小了很多，耗时更少**。
### Bias-Variance Trade-Off for k-Fold Cross-Validation
K的选取是一个Bias和Variance的trade-off。  
> K越大，每次投入的训练集的数据越多，模型的Bias越小。  
但是K越大，又意味着每一次选取的训练集之间的相关性越大（考虑最极端的例子，当k=N，也就是在LOOCV里，每次都训练数据几乎是一样的）。而这种大相关性会导致最终的test error具有更大的Variance。

一般来说，**根据经验我们一般选择k=5或10**。

### Cross-Validation on Classification Problems
上面我们讲的都是回归问题，所以用MSE来衡量test error。  
如果是分类问题，那么我们可以用以下式子来衡量Cross-Validation的test error：

&nbsp;
## reference
[Cross-Validation（交叉验证）详解](https://zhuanlan.zhihu.com/p/24825503)
