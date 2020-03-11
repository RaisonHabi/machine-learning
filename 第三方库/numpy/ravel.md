## numpy.ravel(a, order='C')
Return a contiguous flattened array.  

A 1-D array, containing the elements of the input, is returned. A copy is made only if needed.

## It is equivalent to reshape(-1, order=order)
## examples
```
>>> x = np.array([[1, 2, 3], [4, 5, 6]])
>>> np.ravel(x)
array([1, 2, 3, 4, 5, 6])
>>>
>>> x.reshape(-1)
array([1, 2, 3, 4, 5, 6])
>>>
>>> np.ravel(x, order='F')
array([1, 4, 2, 5, 3, 6])
```

When order is ‘A’, it will preserve the array’s ‘C’ or ‘F’ ordering:
```
>>>
>>> np.ravel(x.T)
array([1, 4, 2, 5, 3, 6])
>>> np.ravel(x.T, order='A')
array([1, 2, 3, 4, 5, 6])
```
## reference
[numpy.ravel](https://docs.scipy.org/doc/numpy/reference/generated/numpy.ravel.html)
