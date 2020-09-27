### 1.密度分布
```
import seaborn as sns

plt.figure(figsize=(15, 10)) ##设置画布大小
sns.kdeplot(df_3_new[13])
sns.kdeplot(df_4_new[13])
sns.kdeplot(df_5_new[13])
sns.kdeplot(df_6_new[13])
sns.kdeplot(df_7_new[13])
plt.show()
```

## reference  
[matplotlib.pyplot.plot](https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.plot.html)  
[matplotlib.pyplot.plot()参数详解](https://blog.csdn.net/sinat_36219858/article/details/79800460)
