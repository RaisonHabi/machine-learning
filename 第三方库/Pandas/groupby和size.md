## groupby
Group DataFrame using a mapper or by a Series of columns.

```
df = pd.DataFrame({'Animal': ['Falcon', 'Falcon',
                              'Parrot', 'Parrot'],
                   'Max Speed': [380., 370., 24., 26.]})
df
   Animal  Max Speed
0  Falcon      380.0
1  Falcon      370.0
2  Parrot       24.0
3  Parrot       26.0
df.groupby(['Animal']).mean()
        Max Speed
Animal
Falcon      375.0
Parrot       25.0
```

&nbsp;
## size
Return an int representing the number of elements in this object.返回元素个数

Return the number of rows if Series. Otherwise return the number of rows times number of columns if DataFrame.  
series返回行数，df返回元素个数
```
s = pd.Series({'a': 1, 'b': 2, 'c': 3})
s.size
3

df = pd.DataFrame({'col1': [1, 2], 'col2': [3, 4]})
df.size
4
```

&nbsp;

## 列里某值出现的次数
```
x=[{"id": "z", 'name':"q"},{"id": "v", 'name':"q"},{"id": "a", 'name':"q"},{"id": "z", 'name':"o"}]
p =pd.DataFrame(x)
In [46]: p
Out[46]:
 id name
0 z q
1 v q
2 a q
3 z o

In [47]: p.groupby(['id']).size()['z']
Out[47]: 2

In [48]: p.groupby(['name']).size()['q']
Out[48]: 3
```

&nbsp;
## reference
[pandas.DataFrame.groupby](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html)  
[pandas.DataFrame.size](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.size.html).   
[pandas统计列里某值出现的次数](https://www.jianshu.com/p/de1a13532d05)
