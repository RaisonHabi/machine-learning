## header: int or list of ints, default ‘infer’  
指定行数用来作为列名，数据开始行数。  
如果文件中没有列名，则默认为0，否则设置为None。  
如果明确设定header=0 就会替换掉原来存在列名。  
header参数可以是一个list例如：[0,1,3]，这个list表示将文件中的这些行作为列标题（意味着每一列有多个标题），介于中间的行将被忽略掉（例如本例中的2；本例中的数据1,2,4行将被作为多级标题出现，第3行数据将被丢弃，dataframe的数据从第5行开始。）。

注意：如果skip_blank_lines=True 那么header参数忽略注释行和空行，所以header=0表示第一行数据而不是文件的第一行。

&nbsp;
## low_memory:boolean, default True  
分块加载到内存，再低内存消耗中解析。  
**但是可能出现类型混淆。确保类型不被混淆需要设置为False**。  
或者使用dtype 参数指定类型。注意使用chunksize 或者iterator 参数分块读入会将整个文件读入到一个Dataframe，而忽略类型（只能在C解析器中有效）

&nbsp;
## 参数dtype
**Type name or dict of column -> type, optional**    
Data type for data or columns. E.g. {‘a’: np.float64, ‘b’: np.int32, ‘c’: ‘Int64’} Use str or object together with suitable na_values settings to preserve and not interpret dtype. If converters are specified, they will be applied INSTEAD of dtype conversion.

&nbsp;
## nrows
nrows: *int, optional*    
Number of rows of file to read. Useful for reading pieces of large files.

&nbsp;
## reference
[pandas.read_csv参数详解](https://www.cnblogs.com/datablog/p/6127000.html)  
[pandas.read_csv](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html)
