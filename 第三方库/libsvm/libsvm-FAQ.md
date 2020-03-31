frequently asked questions (FAQs)  
## Data_preparation
### Q: Why sometimes not all attributes of a data appear in the training/model files ?
libsvm uses the so called **"sparse" format** where zero values do not need to be stored.  
Hence a data with attributes  
1 0 2 0  
is represented as  
1:1 3:2  
### Q: What if my data are non-numerical ?
Currently libsvm **supports only numerical data**. You may have to change non-numerical data to numerical.  
For example, you can use several binary attributes to represent a categorical attribute.
### Q: Why do you consider sparse format ? Will the training of dense data be much slower ?
This is a controversial issue.   
The kernel evaluation (i.e. inner product) of **sparse vectors is slower** so the total training time can be **at least twice or three times** of that using the dense format.  
However, we cannot support only dense format as then we CANNOT handle extremely sparse cases. Simplicity of the code is another concern. Right now we decide to support the sparse format only.
### Q: Why sometimes the last line of my data is not read by svm-train?
We assume that you have **'\n'** in the end of each line.   
So please **press enter in the end of your last line**.
### Q: How to convert other data formats to LIBSVM format?
It depends on your data format. A simple way is to use libsvmwrite in the libsvm matlab/octave interface. Take a CSV (comma-separated values) file in UCI machine learning repository as an example. We download SPECTF.train. Labels are in the first column. The following steps produce a file in the libsvm format.
```
matlab> SPECTF = csvread('SPECTF.train'); % read a csv file
matlab> labels = SPECTF(:, 1); % labels from the 1st column
matlab> features = SPECTF(:, 2:end); 
matlab> features_sparse = sparse(features); % features must be in a sparse matrix
matlab> libsvmwrite('SPECTFlibsvm.train', labels, features_sparse);
The tranformed data are stored in SPECTFlibsvm.train.
```
Alternatively, you can use convert.c to convert CSV format to libsvm format.  

注：python3暂无接口，需自己写或改写csv2libsvm.py  
[使用python将CSV文件转换为LIBSVM兼容数据文件](https://www.thinbug.com/q/23170152)  

&nbsp;
## reference
libsvm-3.24/FAQ.html#/
