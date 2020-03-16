## DataFrame.sample
(self: ~FrameOrSeries, n=None, frac=None, replace=False, weights=None, random_state=None, axis=None) → ~FrameOrSeries

**Parameters**  
> ***n***:int, optional  
Number of items from axis to return. Cannot be used with frac. Default = 1 if frac = None.  
***frac***:float, optional  
Fraction of axis items to return. Cannot be used with n.  
***replace***:bool, default False  
Allow or disallow sampling of the same row more than once.  
***weights***:str or ndarray-like, optional  
Default ‘None’ results in equal probability weighting. If passed a Series, will align with target object on index. Index values in weights not found in sampled object will be ignored and index values in sampled object not in weights will be assigned weights of zero. If called on a DataFrame, will accept the name of a column when axis = 0. Unless weights are a Series, weights must be same length as axis being sampled. If weights do not sum to 1, they will be normalized to sum to 1. Missing values in the weights column will be treated as zero. Infinite values not allowed.

***random_state***:int or numpy.random.RandomState, optional  
Seed for the random number generator (if int), or numpy RandomState object.  
***axis***:{0 or ‘index’, 1 or ‘columns’, None}, default None  
Axis to sample. Accepts axis number or name. Default is stat axis for given data type (0 for Series and DataFrames).
## reference
[pandas.DataFrame.sample](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.sample.html)
