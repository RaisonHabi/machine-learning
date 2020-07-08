## value_counts
Series.value_counts(self, normalize=False, sort=True, ascending=False, bins=None, dropna=True)

### 1.Parameters:  
```
normalize:bool, default False
If True then the object returned will contain the relative frequencies of the unique values.

sort:bool, default True
Sort by frequencies.

ascending:bool, default False
Sort in ascending order.

bins:int, optional
Rather than count values, group them into half-open bins, a convenience for pd.cut, only works with numeric data.

dropna:bool, default True
Don’t include counts of NaN.
```
### 2.示例：
```
# 默认按频数倒序
print(data[2].value_counts())
>>>
1.0    38455
0.0     4542
Name: 2, dtype: int64

# 按index排序
print(data[2].value_counts().sort_index())
>>>
0.0     4542
1.0    38455
Name: 2, dtype: int64
```
```
s = pd.Series([3, 1, 2, 3, 4, np.nan])

# normalize返回频率
s.value_counts(normalize=True)
3.0    0.4
4.0    0.2
2.0    0.2
1.0    0.2
dtype: float64

# bins
s.value_counts(bins=3)
(2.0, 3.0]      2
(0.996, 2.0]    2
(3.0, 4.0]      1
dtype: int64

# dropna
s.value_counts(dropna=False)
3.0    2
NaN    1
4.0    1
2.0    1
1.0    1
dtype: int64
```



&nbsp;
## reference
[pandas.Series.value_counts](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.value_counts.html)
