## pandas.DataFrame.corr

Compute pairwise correlation of columns, excluding NA/null values.

Parameters
```
method: {‘pearson’, ‘kendall’, ‘spearman’} or callable
    Method of correlation:
        pearson : standard correlation coefficient
        kendall : Kendall Tau correlation coefficient
        spearman : Spearman rank correlation
        callable: callable with input two 1d ndarrays
            and returning a float. Note that the returned matrix from corr 
            will have 1 along the diagonals and will be symmetric regardless of the callable’s behavior.
            New in version 0.24.0.

min_periods: int, optional
    Minimum number of observations required per pair of columns to have a valid result.
    Currently only available for Pearson and Spearman correlation.
```
Returns: DataFrame  
Correlation matrix.

Examples
```
def histogram_intersection(a, b):
    v = np.minimum(a, b).sum().round(decimals=1)
    return v
df = pd.DataFrame([(.2, .3), (.0, .6), (.6, .0), (.2, .1)],
                  columns=['dogs', 'cats'])
df.corr(method=histogram_intersection)
      dogs  cats
dogs   1.0   0.3
cats   0.3   1.0
```

&nbsp;
## reference
[pandas.DataFrame.corr](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.corr.html)
