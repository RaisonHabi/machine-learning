## 一、pivot_table
pivot_table有四个最重要的参数index、values、columns、aggfunc，本文以这四个参数为中心讲解pivot操作是如何进行。

### Index
### values
Values可以对需要的计算数据进行筛选
### 示例
```
df
     A    B      C  D  E
0  foo  one  small  1  2
1  foo  one  large  2  4
2  foo  one  large  2  5
3  foo  two  small  3  5
4  foo  two  small  3  6
5  bar  one  large  4  6
6  bar  one  small  5  8
7  bar  two  small  6  9
8  bar  two  large  7  9
```
```
table = pd.pivot_table(df, values='D', index=['A', 'B'],columns=['C'], aggfunc=np.sum)
>>> table
C        large  small
A   B
bar one    4.0    5.0
    two    7.0    6.0
foo one    4.0    1.0
    two    NaN    6.0
```
```
table = pd.pivot_table(df, values=['D', 'E'], index=['A', 'C'],
                    aggfunc={'D': np.mean,
                             'E': np.mean})
>>> table
                D         E
A   C
bar large  5.500000  7.500000
    small  5.500000  8.500000
foo large  2.000000  4.500000
    small  2.333333  4.333333
```
```
table = pd.pivot_table(df, values=['D', 'E'], index=['A', 'C'],
                    aggfunc={'D': np.mean,
                             'E': [min, max, np.mean]})
>>> table
                D    E
            mean  max      mean  min
A   C
bar large  5.500000  9.0  7.500000  6.0
    small  5.500000  9.0  8.500000  8.0
foo large  2.000000  5.0  4.500000  4.0
    small  2.333333  6.0  4.333333  2.0
```

&nbsp;
## 二、query
由pivot_table生成的表查询
```
table.query('对手 == ["灰熊"]')
```

&nbsp;
## 三、pivot_table vs. groupby
```
pd.pivot_table(df,index=[字段1],values=[字段2],aggfunc=[函数],fill_value=0)
df.groupby([字段1])[字段2].agg(函数).fillna(0)
```
上面两个函数完全等价，pivot_table仿佛是加入了columns与margin功能的groupby函数，比groupby更加灵活。

&nbsp;
## reference
[Pandas | 一文看懂透视表pivot_table](https://zhuanlan.zhihu.com/p/31952948)  
[pandas.pivot_table](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.pivot_table.html)
