### NumPy
* use to represent arrays of number
* Only store a SINGLE type of data; FIXED TYPE; (homogenous)
* allows most efficient code and memory representation
* Size is fixed (no append()/remove())
    - No append method to this array, but can concatenate
```py
>>> import numpy
>>> a = numpy.array([7, 42, -3])
>>> a
array([ 7, 42, -3])
>>> a[1]
42
>>> a[1] = 19
>>> a
array([ 7, 19, -3])
>>>
```

### Data types

* Integers

    * 8, 16, 32, and 64 bit signed and unsigned (numpy.int8, numpy.uint8, etc.)

* Floating point

    * 32, 64, 128 bit (numpy.float32, numpy.float64, etc.)

* Complex, strings, and Python object references also supported, but these cause overhead

Data is compactly stored, so we can quickly access next number in the array. Stores in a consecutive block of memory, so quicker

**contiguous memory**

C stores row major ordering
Fortran stores column major ordering

### Data type examples
* Numerical python types are instances of dtype

```py
>>> a = numpy.array([ 7, 19, -3], dtype=numpy.float32)
>>> a
array([ 7., 19., -3.], dtype=float32)
>>> a[0] = a[0]/0.
__main__:1: RuntimeWarning: divide by zero encountered
>>> a
array([ inf, 19., -3.], dtype=float32)
>>> b = numpy.array([4, 7, 19], dtype=numpy.int8)
>>> b
array([ 4, 7, 19], dtype=int8)
>>> b[0] = 437
>>> b
array([-75, 7, 19], dtype=int8)
>>>
```
be careful to specify the type of array
can represent infinity
division by 0 gives warning, but doesn't stop code
### Multidimensional arrays

* Arrays can have multiple dimensions called *axes*

* The number of *axes* is called the *rank*

### Multidimensional arrays

```py
>>> a = numpy.array([(7, 19, -3), (4, 8, 17)], dtype=numpy.float64)
>>> a
array([[ 7., 19., -3.],
[ 4., 8., 17.]])
>>> a.ndim
2
>>> a.dtype
dtype('float64')
>>> a.shape #shape of matrix (rows, cols)
(2, 3)
>>> a.size #finds total number of elements in array
6
>>>
```
- same as matlab, first index is column, next is rows
- last index is contiguous in memory (rows are contiguous)
### Internal representation

![fig](lecture-10/fig/numpy-representation.png)

contiguous block of memory- data is together, so high performance

### Creating arrays

.empty doesn't create empty matrix, just random numbers
need to pass zeros as a tuple
shape gives tuple of shape of dimensions

```py
>>> a = numpy.empty((3,3))
>>> a
array([[  2.12261410e-314,   0.00000000e+000,   2.14827413e-314],
       [  2.14834326e-314,   2.14832351e-314,   2.14834284e-314],
       [  0.00000000e+000,   0.00000000e+000,   2.12336647e-314]])
>>> a = numpy.zeros((3,3))
>>> a
array([[ 0.,  0.,  0.],
       [ 0.,  0.,  0.],
       [ 0.,  0.,  0.]])
>>> a = numpy.ones((3,3))
>>> a
array([[ 1.,  1.,  1.],
       [ 1.,  1.,  1.],
       [ 1.,  1.,  1.]])
>>> a = numpy.eye(3)
>>> a
array([[ 1.,  0.,  0.],
       [ 0.,  1.,  0.],
       [ 0.,  0.,  1.]])
>>> a=numpy.random.rand(3,2)
>>> a
array([[ 0.38009444,  0.03368697],
       [ 0.239883  ,  0.03853005],
       [ 0.30929021,  0.16703058]])

```
### Sum Across rows or columns
```py
>>> A
array([[ 1,  4,  5],
       [ 7, 10,  0],
       [ 0,  1,  7]])

#Sum across rows
>>> A.sum(axis=1)
array([10, 17,  8])
>>> sum(A.transpose())
array([10, 17,  8])

#Sum across columns
>>> A.sum(axis=0)
array([ 8, 15, 12])
>>> sum(A)
array([ 8, 15, 12])

```

### Creating more arrays
can reshape matrix- change rank; product of dimensions should match (can't change amount of data)
By default, they use C ordering (rows are contiguous in memory)

```py
>>> a = numpy.arange(9, dtype=numpy.float64)
>>> a
array([ 0., 1., 2., 3., 4., 5., 6., 7., 8.])
>>> a = numpy.arange(9, dtype=numpy.float64).reshape(3,3)
>>> a
array([[ 0., 1., 2.],
[ 3., 4., 5.],
[ 6., 7., 8.]])
>>>
```
### Fortran ordering
default is that array orders by row, but if we do fortran ordering,columns will be contiguous
```py
>>> a = numpy.arange(9,dtype=numpy.float64).reshape((3,3))
>>> a
array([[ 0., 1., 2.],
       [ 3., 4., 5.],
       [ 6., 7., 8.]])
>>> a = numpy.arange(9,dtype=numpy.float64).reshape((3,3)),order='F')
array([[ 0., 3., 6.],
       [ 1., 4., 7.],
       [ 2., 5., 8.]])
```
### Creating more arrays

```
$ cat numbers.txt
7. 19. -3.
4. 8. 17.
$ python
Python 2.7.5+ (default, Feb 27 2014, 19:37:08)
[GCC 4.8.1] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import numpy
>>> a = numpy.loadtxt('numbers.txt', dtype=numpy.float64)
>>> a
array([[ 7., 19., -3.],
[ 4., 8., 17.]])
>>> numpy.savetxt('numbers2.txt', a) #dumps info to disk
>>>
```
### Other Functions: reshape, arange, linspace, indices
```py
>>> a=numpy.arange(10)
>>> a
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> numpy.arange(3,10) #I want to increment by 1 from (a,b) not including b
array([3, 4, 5, 6, 7, 8, 9])
>>> a=numpy.arange(10,1,-2) #go backwards from 10 to 1 (don't include 1), skip every 2
>>> a
array([10,  8,  6,  4,  2])


>>> a.reshape(2,5) #I want to reshape array into (row,columns)
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9]])
#Or, can also do
>>> x.shape = (2,5)
>>> x
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9]])


>>> numpy.linspace(0,1,11) #I want 11 numbers between 0 and 1 (inclusive)
array([ 0. ,  0.1,  0.2,  0.3,  0.4,  0.5,  0.6,  0.7,  0.8,  0.9,  1. ])

>>> numpy.indices((4,2)) #get indices of 4x2 matrix (row array,col array)
array([[[0, 0],
        [1, 1],
        [2, 2],
        [3, 3]],

       [[0, 1],
        [0, 1],
        [0, 1],
        [0, 1]]])

```
### Squeeze and reshape
```py
>>> import numpy as np
>>> a = numpy.arange(3)
>>> a
array([0, 1, 2])
>>> a.shape
(3,)
>>> b = numpy.arange(3).reshape(3,1)
>>> b
array([[0],
[1],
[2]])
>>> b.shape
(3, 1)
>>> b = numpy.squeeze(b)  #squeeze function gets rid of singleton dimensions
>>> b
array([0, 1, 2])
>>> b.shape
(3,)     #created a 1D numpy array, this is a python tuple with single element
>>> b = np.arrange(3).reshape(3,1)
>>> b.shape
(3,1)
>>> b.ndim   
2
```
array broadcasting: system with a bunch of rules (for example, outer product addition)
  -keep in mind in case you get unexpected behavior

### Slicing and Indexing

```py
>>> a = numpy.arange(9, dtype=numpy.float64)
>>> a
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.])
>>> a[-2] #access from behind
7.0
>>> a[3:7] # to get subarray (don't include 7)
array([ 3.,  4.,  5.,  6.])
>>> a[3:7] = 0 #can assign to a subsequence (don't include 7)

#Get columns or row of 2D array
>>> x
array([[0, 1, 2, 3, 4],
       [5, 6, 7, 8, 9]])
>>> x[:,1] #get col
array([1, 6])
>>> x[0,:] #get row
array([0, 1, 2, 3, 4])

#Get submatrix
>>> a=numpy.array([(1,4,6),(4,9,10)])
>>> a
array([[ 1,  4,  6],
       [ 4,  9, 10]])
>>> a[:,0:2] #first two columns
array([[1, 4],
       [4, 9]])
>>> a
array([[0, 1, 2, 3, 4],
      [5, 6, 7, 8, 9]])
>>> a[:,0:5:2] #get every other column (between 0 and 5, skip 2)
array([[0, 2, 4],
      [5, 7, 9]])
>>> a
array([[0, 1, 2, 3, 4],
      [5, 6, 7, 8, 9]])
>>> a[1,::2] #row 1, ::2 means from beginning to end, skip every 2
array([5, 7, 9])
```
### Slicing operation returns a ref to subarray rather than a subarray
* if we change b, it changes a
```py
>>> a=numpy.arange(9)
>>> a
array([0, 1, 2, 3, 4, 5, 6, 7, 8])
>>> b=a[3:7]
>>> b
array([3, 4, 5, 6])
>>> b[0]=0
>>> b
array([0, 4, 5, 6])
>>> a
array([0, 1, 2, 0, 4, 5, 6, 7, 8])
>>>
```
* Fix by creating array copies
 - note that deepcopy() also works
```py
>>> b=numpy.copy(a[3:7])
>>> b
array([3, 4, 5, 6])
>>> b[0]=0
>>> b
array([0, 4, 5, 6])
>>> a
array([0, 1, 2, 3, 4, 5, 6, 7, 8])
#or use shorthand
>>> a=numpy.array([3,2,1])
>>> b=numpy.zeros((1,3))
>>> b[:]=a[:]

```

### Sum, Max, Min
```py
>>> a
array([ 0.,  1.,  2.,  0.,  0.,  0.,  0.,  7.,  8.])
>>> 2*a
array([  0.,   2.,   4.,   0.,   0.,   0.,   0.,  14.,  16.])
>>> a*a
array([  0.,   1.,   4.,   0.,   0.,   0.,   0.,  49.,  64.])
>>> sum(a) #or can do a.sum()
18.0
>>> min(a)
0.0
>>> max(a)
8.0
>>>
```
### Array operations
- Find norm of vector example
```py
>>> import math
>>> a = numpy.arange(9, dtype=numpy.float64)
>>> a
array([ 0., 1., 2., 3., 4., 5., 6., 7., 8.])
>>> # bad idea
>>> total = 0.
>>> for n in range(len(a)):
...   total += a[n]*a[n]
...
>>> math.sqrt(total)
14.2828568570857
>>> # better idea
>>> math.sqrt(numpy.dot(a,a))
14.2828568570857
>>> # best idea
>>> numpy.linalg.norm(a)
14.282856857085701
>>>
```
-Square root, vector dot product
```py
>>> a = numpy.arange(9, dtype=numpy.float64)
>>> a
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.])
>>> numpy.sqrt(a) #square root of all elements
array([ 0.        ,  1.        ,  1.41421356,  1.73205081,  2.        ,
        2.23606798,  2.44948974,  2.64575131,  2.82842712])


>>> v1=numpy.array([1,2,3]) #two vector dot products
>>> v2=numpy.array([3,1,5])
>>> v3=numpy.vdot(v1,v2)
>>> v3
20
>>> (1*3)+(1*2)+(3*5)
20
```
LINEAR ALGEBRA: http://docs.scipy.org/doc/numpy-1.10.0/reference/routines.linalg.html

### Matrix-Vector operations
```py
>>> A=numpy.arange(9).reshape(3,3)
>>> A
array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
>>> b=numpy.array([[2],[1],[0]])
>>> b
array([[2],
       [1],
       [0]])
>>> A*b #MISLEADING: multiplies top row of A by 2, middle by 1, last by 0
array([[0, 2, 4],
       [3, 4, 5],
       [0, 0, 0]])
#I actually want this:
>>> A.dot(b)
array([[ 1],
       [10],
       [19]])
```

### Matrix operations: Transpose, Trace, matrix-matrix mult

```py
>>> a = numpy.arange(9, dtype=numpy.float64).reshape(3,3)
>>> a
array([[ 0.,  1.,  2.],
       [ 3.,  4.,  5.],
       [ 6.,  7.,  8.]])
>>> a.transpose()
array([[ 0.,  3.,  6.],
       [ 1.,  4.,  7.],
       [ 2.,  5.,  8.]])
>>> numpy.trace(a)
12.0
>>> a*a # element wise multiplication
array([[  0.,   1.,   4.],
       [  9.,  16.,  25.],
       [ 36.,  49.,  64.]])
>>> numpy.dot(a,a) # matrix-matrix multiplication
array([[  15.,   18.,   21.],
       [  42.,   54.,   66.],
       [  69.,   90.,  111.]])

```

### References to an array

```py
>>> a = numpy.arange(9, dtype=numpy.float64).reshape(3,3)
>>> a
array([[ 0.,  1.,  2.],
       [ 3.,  4.,  5.],
       [ 6.,  7.,  8.]])
>>> b = a
>>> b[0,0] = 42
>>> b
array([[ 42.,   1.,   2.],
       [  3.,   4.,   5.],
       [  6.,   7.,   8.]])
>>> a
array([[ 42.,   1.,   2.],
       [  3.,   4.,   5.],
       [  6.,   7.,   8.]])
>>>
```
### For Fun, Conjugate Gradient Method

```py
import numpy as np
import scipy
file = np.loadtxt('matrix1Karen.dat')
row = file[:,0]
col = file[:,1]
val = file[:,2]
#print(file)
#print(row)
#print(col)
#print(val)
M = 2
N = 2
#print(M)
#print(N)
#A = scipy.sparse.coo_matrix((val, (row, col)), shape=(M, N)).toarray()
b = np.zeros((M,1))
#x = np.linalg.solve(A,b)
#print(x)

####
#CG Method
x = np.ones((M,1))
r = b - np.dot(A,x)
L2norm0 = np.linalg.norm(r,2)
p = r.copy()
niter = 0

nitermax = 1000
tol = 1e-5
L2normr = 0
alpha=0
beta=0
dotprodr=0


while (niter < nitermax):
  niter += 1
  dotprodr = np.vdot(r,r)
  alpha = dotprodr/(np.vdot(p,np.dot(A,p)))
  x += alpha*p
  r -= alpha*np.dot(A,p)
  L2normr = np.linalg.norm(r,2)
  if (L2normr/L2norm0) < tol:
      break
  beta = np.vdot(r,r)/dotprodr
  print(beta)
  p = r + beta*p

print(x)
print("SUCCESS: CG solver converged in {} iterations").format(niter)

```


### NumPy, SciPy, matplotlib

* use compiled code for fast tasks

* SciPy - Various math functionality (linear solvers, FFT, optimization, etc.)
utilizing NumPy arrays

* matplotlib - plotting and data visualization

* NumPy- multidimensional arrays and fundamental operations on them

    * Searching, sorting, and counting within arrays

    * FFT (Fast Fourier Transform)

    * Linear Algebra

    * Statistics

    * Polynomials

    * Random number generation

* SciPy has largely replaced much of this functionality,
plus added much more
### Further Reading

* MATLAB users: <http://www.scipy.org/NumPy_for_Matlab_Users>
* NumPy tutorial at: <http://www.scipy.org/Tentative_NumPy_Tutorial>
* Official docs at: <http://docs.scipy.org/>
