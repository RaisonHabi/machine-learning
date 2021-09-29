## numpy.ones
### numpy.ones(shape, dtype=None, order='C', *, like=None)
Return a new array of given shape and type, filled with ones.
### 示例
```
np.ones(6)
out: array([1., 1., 1., 1., 1., 1.])

np.ones([1,6])
out: array([[1., 1., 1., 1., 1., 1.]])

np.ones([6,1])
out: array([[1.],
       [1.],
       [1.],
       [1.],
       [1.],
       [1.]])
```
```
a=np.ones(6)
b=np.zeros([6,2])
np.c_[a,b]

out: array([[1., 0., 0.],
       [1., 0., 0.],
       [1., 0., 0.],
       [1., 0., 0.],
       [1., 0., 0.],
       [1., 0., 0.]])
```

&nbsp;
## References
[numpy.ones](https://numpy.org/doc/stable/reference/generated/numpy.ones.html)   
[numpy.reshape](https://numpy.org/doc/stable/reference/generated/numpy.reshape.html)
