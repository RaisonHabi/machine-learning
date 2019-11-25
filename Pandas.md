## dropna
DataFrame.dropna(self, axis=0, how='any', thresh=None, subset=None, inplace=False)  
Remove missing values.   
**parameters**  
> **axis : {0 or ‘index’, 1 or ‘columns’}, default 0**    
Determine if rows or columns which contain missing values are removed.  
0, or ‘index’ : Drop rows which contain missing values.  
1, or ‘columns’ : Drop columns which contain missing value.  
*Deprecated since version 0.23.0: Pass tuple or list to drop on multiple axes. Only a single axis is allowed.*  
**how : {‘any’, ‘all’}, default ‘any’**  
Determine if row or column is removed from DataFrame, when we have at least one NA or all NA.  
‘any’ : If any NA values are present, drop that row or column.  
‘all’ : If all values are NA, drop that row or column.  
**thresh : int, optional**  
Require that many non-NA values.  
**subset : array-like, optional**  
Labels along other axis to consider, e.g. if you are dropping rows these would be a list of columns to include.  
**inplace : bool, default False**  
If True, do operation inplace and return None.
## fillna
DataFrame.fillna(self, value=None, method=None, axis=None, inplace=False, limit=None, downcast=None, **kwargs)  
Fill NA/NaN values using the specified method. 
**parameters**  
> **value : scalar, dict, Series, or DataFrame**  
Value to use to fill holes (e.g. 0), alternately a dict/Series/DataFrame of values specifying which value to use for each index (for a Series) or column (for a DataFrame). Values not in the dict/Series/DataFrame will not be filled. This value cannot be a list.  
**method : {‘backfill’, ‘bfill’, ‘pad’, ‘ffill’, None}, default None**  
Method to use for filling holes in reindexed Series pad / ffill: propagate last valid observation forward to next valid backfill / bfill: use next valid observation to fill gap.  
**axis : {0 or ‘index’, 1 or ‘columns’}**  
Axis along which to fill missing values.  
**inplace : bool, default False**  
If True, fill in-place. Note: this will modify any other views on this object (e.g., a no-copy slice for a column in a DataFrame).  
**limit : int, default None**  
If method is specified, this is the maximum number of consecutive NaN values to forward/backward fill. In other words, if there is a gap with more than this number of consecutive NaNs, it will only be partially filled. If method is not specified, this is the maximum number of entries along the entire axis where NaNs will be filled. Must be greater than 0 if not None.  
**downcast : dict, default is None**  
A dict of item->dtype of what to downcast if possible, or the string ‘infer’ which will try to downcast to an appropriate equal type (e.g. float64 to int64 if possible).
## reference
[DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/frame.html)  
[pandas 缺失值与空值处理](https://blog.csdn.net/lwgkzl/article/details/80948548)
