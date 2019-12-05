## iloc、loc以及icol
**选取标签为A和C的列，并且选完类型还是dataframe**  
df = df.loc[:, ['A', 'C']]  
df = df.iloc[:, [0, 2]]  
*"，"前面的"："表示选取整列*  

**选取标签为C并且只取前两行，选完类型还是dataframe**  
df = df.loc[0:2, ['A', 'C']]    
df = df.iloc[0:2, [0, 2]]   
*0:2表示选取第0行到第二行，这里的0:2相当于[0,2）前闭后开，2是不在范围之内的*  

***loc是根据dataframe的具体标签选取列，而iloc是根据标签所在的位置，从0开始计数。***   

**df.icol(i)来选取列，选取完的不是dataframe而是series，i为该列所在的位置，从0开始计数。**  
