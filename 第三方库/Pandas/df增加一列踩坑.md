## df增加列错误方式
df['user_id']=data_02['user_id']#直接把某一dataframe的一列赋给另一个dataframe  
## 正确方式
df['user_id']=list(data_02['user_id'])  
df['user_id']=np.array(data_02['user_id'])  
