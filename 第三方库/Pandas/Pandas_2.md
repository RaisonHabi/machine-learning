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
