## np.r_
**行数row增多，就是把两矩阵上下相加，要求列数相等，类似于pandas中的concat()**  
## np.c_
**列数col增多，就是把两矩阵左右相加，要求行数相等，类似于pandas中的merge()**  
## 示例
**示例1**  
> import numpy as np  
a = np.array([[1, 2, 3],[4,5,6]])  
b = np.array([[0, 0, 0],[1,1,1]])   

> *print(a)*    
[[1 2 3]  
[4 5 6]]  
*print(b)*     
[[0 0 0]  
[1 1 1]]  
*print(**np.r_[a,b]**)*    
[[1 2 3]  
[4 5 6]  
[0 0 0]  
[1 1 1]]  
*print(**np.c_[a,b]**)*    
[[1 2 3 0 0 0]  
[4 5 6 1 1 1]]  

**示例2：**  
> import numpy as np  
a = np.array([1, 2, 3])  
b = np.array([4, 5, 6])  
c = np.c_[a,b]  

> *print(a)*  
[1 2 3]**(列向量,3行1列)**   
*print(np.r_[a,b])*    
[1 2 3 4 5 6] **(列向量,6行1列)**     
*print(c)*    
[[1 4]  
 [2 5]  
 [3 6]]  
*print(np.c_[c,a])*    
[[1 4 1]  
 [2 5 2]  
 [3 6 3]]  
**在numpy中，一个列表虽然是横着表示的，但它是列向量。**  

[numpy中np.c_和np.r_](https://blog.csdn.net/yj1556492839/article/details/79031693)
