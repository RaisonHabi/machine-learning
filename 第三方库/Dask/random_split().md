## random_split(frac, random_state=None)
Pseudorandomly split dataframe into different pieces row-wise

Parameters:	  
> **frac:list**    
List of floats that should sum to one.

> **random_state: int or np.random.RandomState**  
If int create a new RandomState with this as the seed

Otherwise draw from the passed RandomState
## Examples
> a, b = df.random_split([0.5, 0.5]) 

> a, b, c = df.random_split([0.8, 0.1, 0.1], random_state=123)
