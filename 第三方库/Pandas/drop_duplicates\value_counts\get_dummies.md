## drop_duplicates
**DataFrame.drop_duplicates(self, subset=None, keep='first', inplace=False)**  
Return DataFrame with duplicate rows removed, optionally only considering certain columns.   
Indexes, including time indexes are ignored.

Parameters:	  
> subset : *column label or sequence of labels, optional*    
Only consider certain columns for identifying duplicates, by default use all of the columns  
keep : *{‘first’, ‘last’, False}, default ‘first’*  
>>  first : Drop duplicates except for the first occurrence.  
last : Drop duplicates except for the last occurrence.  
False : Drop all duplicates.  

> inplace : *boolean, default False*  
Whether to drop duplicates in place or to return a copy  

Returns:	
DataFrame

## value_counts
**pandas.Series.value_counts**  
Series.value_counts(*self, normalize=False, sort=True, ascending=False, bins=None, dropna=True*)  
Return a Series containing counts of unique values
## count()
**count():** simply returns the number of non NaN/Null values in column (series) you apply it on.  
**value_counts():** aggregates the data and counts each unique value.   
You can achieve the same by using groupby which is a more broad function to aggregate data in pandas.  
示例：  
> df['Id'].value_counts() 等同于 df.groupby('Id')['Id'].count()  
df['Id'].count()：8

## get_dummies
pandas.get_dummies(***data, prefix=None, prefix_sep='_', dummy_na=False, columns=None, sparse=False, drop_first=False, dtype=None***)   
**Convert categorical variable into dummy/indicator variables.**   
**Parameters:**   
> **data** : *array-like, Series, or DataFrame*   
Data of which to get dummy indicators.  
**prefix** : *str, list of str, or dict of str, default None*  
String to append DataFrame column names.   
Pass a list with length equal to the number of columns when calling get_dummies on a DataFrame.   
Alternatively, prefix can be a dictionary mapping column names to prefixes.  
**prefix_sep** : *str, default ‘_’*  
If appending prefix, separator/delimiter to use. Or pass a list or dictionary as with prefix.  
**dummy_na** : *bool, default False*  
Add a column to indicate NaNs, if False NaNs are ignored.  
**columns** : *list-like, default None*  
Column names in the DataFrame to be encoded.    
If columns is None then all the columns with object or category dtype will be converted.  
**sparse** : *bool, default False*  
Whether the dummy-encoded columns should be backed by a SparseArray (True) or a regular NumPy array (False).  
**drop_first** : *bool, default False*  
Whether to get k-1 dummies out of k categorical levels by removing the first level.  
New in version 0.18.0.  
**dtype** : *dtype, default np.uint8*  
Data type for new columns. Only a single dtype is allowed.  
New in version 0.23.0.  

**Returns:**  	
DataFrame  
Dummy-coded data.  



