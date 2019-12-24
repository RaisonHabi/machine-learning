在用python进行科学运算时，常常需要把一个稀疏的np.array压缩，这时候就用到scipy库中的sparse.csr_matrix(csr:Compressed Sparse Row marix) 和sparse.csc_matric(csc:Compressed Sparse Column marix)

## scipy.sparse.csr_matrix
csr_matrix((data, indices, indptr), [shape=(M, N)])   
is the standard CSR representation where the column indices for row i are stored in indices[indptr[i]:indptr[i+1]] and their corresponding values are stored in data[indptr[i]:indptr[i+1]]. If the shape parameter is not supplied, the matrix dimensions are inferred from the index arrays.  
示例：  
> indptr = np.array([0, 2, 3, 6])  
indices = np.array([0, 2, 2, 0, 1, 2])  
data = np.array([1, 2, 3, 4, 5, 6])  
csr_matrix((data, indices, indptr), shape=(3, 3)).toarray()  
array([[1, 0, 2],  
       [0, 0, 3],  
       [4, 5, 6]])  
**#按row行来压缩**   
***#对于第i行，非0数据列是indices[indptr[i]:indptr[i+1]] 数据是data[indptr[i]:indptr[i+1]]***     
#在本例中  
#第0行，有非0的数据列是indices[indptr[0]:indptr[1]] = indices[0:2] = [0,2]  
#数据是data[indptr[0]:indptr[1]] = data[0:2] = [1,2],所以在第0行第0列是1，第2列是2  
#第1行，有非0的数据列是indices[indptr[1]:indptr[2]] = indices[2:3] = [2]  
#数据是data[indptr[1]:indptr[2] = data[2:3] = [3],所以在第1行第2列是3  
#第2行，有非0的数据列是indices[indptr[2]:indptr[3]] = indices[3:6] = [0,1,2]  
#数据是data[indptr[2]:indptr[3]] = data[3:6] = [4,5,6],所以在第2行第0列是4，第1列是5,第2列是6 

## scipy.sparse.csc_matrix
csc_matrix((data, indices, indptr), [shape=(M, N)])   
is the standard CSC representation where the row indices for column i are stored in indices[indptr[i]:indptr[i+1]] and their corresponding values are stored in data[indptr[i]:indptr[i+1]]. If the shape parameter is not supplied, the matrix dimensions are inferred from the index arrays.  
示例：  
> indptr = np.array([0, 2, 3, 6])  
indices = np.array([0, 2, 2, 0, 1, 2])  
data = np.array([1, 2, 3, 4, 5, 6])  
csc_matrix((data, indices, indptr), shape=(3, 3)).toarray()  
array([[1, 0, 4],  
       [0, 0, 5],  
       [2, 3, 6]])  
**#按col列来压缩**  
***#对于第i列，非0数据行是indices[indptr[i]:indptr[i+1]] 数据是data[indptr[i]:indptr[i+1]]***  
#在本例中  
#第0列，有非0的数据行是indices[indptr[0]:indptr[1]] = indices[0:2] = [0,2]  
#数据是data[indptr[0]:indptr[1]] = data[0:2] = [1,2],所以在第0列第0行是1，第2行是2  
#第1行，有非0的数据行是indices[indptr[1]:indptr[2]] = indices[2:3] = [2]  
#数据是data[indptr[1]:indptr[2] = data[2:3] = [3],所以在第1列第2行是3  
#第2行，有非0的数据行是indices[indptr[2]:indptr[3]] = indices[3:6] = [0,1,2]  
#数据是data[indptr[2]:indptr[3]] = data[3:6] = [4,5,6],所以在第2列第0行是4，第1行是5,第2行是6  
