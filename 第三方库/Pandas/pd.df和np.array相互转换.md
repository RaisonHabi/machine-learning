## dataframe转化成array
arr=df.values
## array转化成dataframe
df = pd.DataFrame(arr)
## 示例：
> import pandas as pd  
arr=[[1,2,3],[4,5,6]]  
df=pd.DataFrame(arr)  
print(df)  
arr1=df.values  
print(arr1)  

>  0  1  2  
0  1  2  3  
1  4  5  6  
[[1 2 3]  
 [4 5 6]]  
