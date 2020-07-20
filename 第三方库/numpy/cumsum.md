## cumsum
**numpy.cumsum(a, axis=None, dtype=None, out=None)**  
Return the cumulative sum of the elements along a given axis.

### Parameters
```
a:array_like
Input array.

axis：int, optional
Axis along which the cumulative sum is computed. 
The default (None) is to compute the cumsum over the flattened array.

……
```

### 示例
```
a = np.array([[1,2,3], [4,5,6]])
array([[1, 2, 3],
       [4, 5, 6]])
       
np.cumsum(a)
array([ 1,  3,  6, 10, 15, 21])

np.cumsum(a, dtype=float)     # specifies type of output value(s)
array([  1.,   3.,   6.,  10.,  15.,  21.])
```
```
np.cumsum(a,axis=0)      # sum over rows for each of the 3 columns
array([[1, 2, 3],
       [5, 7, 9]])
       
np.cumsum(a,axis=1)      # sum over columns for each of the 2 rows
array([[ 1,  3,  6],
       [ 4,  9, 15]])
```

&nbsp;
## reference
[numpy.cumsum](https://numpy.org/doc/stable/reference/generated/numpy.cumsum.html)
