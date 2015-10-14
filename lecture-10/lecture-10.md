# CME 211: Lecture 10

Monday, October 12, 2015

Topic: Representation of numbers, Numpy overview

## Announcements

## Quiz 1 Prep

Sample quiz solutions from 2012 and 2013 are posted on Piazza.

* Open book
* Open note
* Open computer (restricted internet use)
    * you may use Python iterpreter
    * use computer to access notes at
     <https://github.com/nwh/cme211-notes>
    * use computer to access Learning Python book
    * use computer to access Python documentation @ python.org
    * use computer to access reference sheet
      <http://www.astro.up.pt/~sousasag/Python_For_Astronomers/Python_qr.pdf>
    * You may not use Google or any other search method to try and find answers

Topics:

* Understanding of low level and high level programming languages
* Use cases for fundamental data types: integers, floating point numbers,
  strings
* basic Python syntax: loops, conditionals, functions, variables
* built-in Python containers: lists, dictionaries, sets
* fundamentals of python's data model
* Python OOP: defining classes, data attributes, methods
* Basics of complexity analysis (as covered in lecture)

Won't quiz on exceptions
No details on modules

Book chapters:

* Chapter 5: Numeric Types
* Chapter 6: The Dynamic Typing Interlude (i.e. references and objects)
* Chapter 7: String Fundamentals
* Chapter 8: Lists and Dictionaries
* Chapter 9: Tuples, Files, and Everything Else
* Chapter 11: Assignments, Expressions, and Prints
* Chapter 12: if Tests and Syntax Rules
* Chapter 13: while and for Loops
* Chapter 16: Function Basics
* Chapter 17: Scopes
* Chapter 18: Arguments
* Chapter 26: OOP: The Big Picture
* Chapter 27: Class Coding Basics

Notes:

* If you want `sqrt()`, remember to `import math` and use `math.sqrt()`.  Other
  than that, we won't test on module stuff.
* We won't test on python exceptions
* We won't test on Numpy, Scipy, or matplotlib (which we cover this week)

## Computer representation of data

* Computers represent and store everything in *binary*

* Binary, a base 2 number system, consists only of 0s and 1s called binary
digits (bits)

* There are 8 bits in a byte

### Simplified model of computer

![fig](fig/model-computer.png)

large sequence of bytes: call that memory or DRAM. In diagram, each box represents a single byte, each byte in memory has an address; usually 0x00 (hexadecimal, base 16)
fast computations done in registers
communication across memory bus is very slow compared to communication from register to alu
nearby data is also brought along in memory bus, so it's ready to go in register
temporal locality: likely i want data soon
spatial locality: likely i want data nearby


### Converting between bases

* One can easily convert numbers between different bases such as binary (base 2)
and decimal (base 10)

```
| decimal | binary |
|---------+--------|
|       0 |      0 |
|       1 |      1 |
|       2 |     10 |
|       3 |     11 |
|       4 |    100 |
|       5 |    101 |
|       6 |    110 |
|       7 |    111 |
```
if we have 8 bits, then largest number is 2^7+2^6...+2^0 = 255, so we have a range from 0-255 or can represent 256.
For negative number, use negative two's compliment

### Common prefixes

* kilo, mega, giga, tera, peta, exa prefixes:

```
| kilobyte (kB) | 10^3 (or 2^10)  |
| megabyte (MB) | 10^6 (or 2^20)  |
| gigabyte (GB) | 10^9 (or 2^30)  |
| terabyte (TB) | 10^12 (or 2^40) |
| petabyte (PB) | 10^15 (or 2^50) |
| exabyte (EB)  | 10^18 (or 2^60) |
```

* Networking and storage typically use base 10 while memory is specified in
terms of base 2

* Technically prefixes and symbols are different, e.g. Kilobyte or kibibyte with
symbols KB / KiB / Kbytes for base 2

### Computer storage of text

* American Standard Code for Information Interchange (ASCII) is typically used
to encode text information

Assigns binary number to each letter, each character represents 7 bits, represent 128 characters

* Characters, numbers, symbols, etc. are encoded using 7 bits (although on
modern computers they would typically use 8 bits)

  * `A` maps to `1000001` in binary or 65 in decimal
  * `B` maps to `1000010` in binary or 66 in decimal

  python 2: strings are ascii
  python 3: strings in unicode format, up to 32 bits

### Computer storage of a number

* At the hardware level computers **don't** do variable length representations of
numbers

* We might write:

  * 4 as `100`, using 3 bits

  * 73 as `1001001`, using 7 bits

### Fixed storage widths

![fig/bits.png](fig/bits.png)

all floating point numbers take same amount of memory (same bits)
### Integer representation

* At the hardware level computers typically handle integers using 8, 16, 32, or
64 bits

![fig/dec-bin-table.png](fig/dec-bin-table.png)

bit shift to left = multiplying the number
bit shift to right = divide, but might lose remainder

### Integer range

* For `n` bits, there are only `2^n` unique combinations of 0s and 1s

* This limits the range of what can be represented with a fixed number of bits

```
2^8  = 256
2^16 = 65536
2^32 = 4294967296
2^64 = 18446744073709551616
```

### Sign bit

* Use one bit for sign and remaining bits for magnitude
cuts your positive values by a power of 2
two representations of 0 and -0, not good
causes hardware implementation to be more complicated

![fig/sign-bit.png](fig/sign-bit.png)

* Reduces the range of the magnitude from `2^n` to `2^(n-1)`

### Offset

* Apply an offset or bias to reinterpret the conversion between binary and
decimal

![fig/sign-offset.png](fig/sign-offset.png)

* Again, effectively reduces the range of the magnitude

LOOK at two's compliment- what's actually used in computers

### Unsigned integers

* Many programming languages support unsigned integers

* Python itself does not have unsigned integers, but Numerical Python (`numpy`) does

* Can use this to your advantage to expand the effective range available if
negative numbers don't need to be stored

* But be careful...

### Overflow and underflow

* Attempting to assign a value greater than what can be represented by the data
type will result in overflow

* Attempting to assigning a value less than what can be represented by the data
type will result in underflow

* Overflow or underflow tend to cause wraparound, e.g. if adding together two
signed numbers causes overflow the result is likely to be a negative number

overflow causes 128+1 = -127; same problem with -127-1 = 128

```py
>>> a = numpy.zeros(1,dtype=numpy.uint32)
>>> a
array([0], dtype=uint32)
>>> a-1
array([4294967295], dtype=uint32)
```
using numpy, give uint (unsigned integer of 32 bits, and subtract 1 to get largest value)
shows that it wraps around

### Range of integer types

![fig/int-range.png](fig/int-range.png)

### Floating point representation

* How do I represent a floating point value using bits?

![fig/float.png](fig/float.png)
10-6 default, up to 6 accuracy

### Floating point standard

* IEEE (Institute of Electrical and Electronics Engineers) 754 is the technical
standard for floating point used by all modern processors

![fig](fig/float-table.png)

* Standard also specifies things like rounding modes, handling overflow, divide
by zero, etc.

### Floating point and you

* Floating point also has similar potential for overflow and underflow

* In addition, the limited number of bits for the mantissa means it often needs
to be rounded

* Will spend more time on floating point arithmetic in CME 212

* What Every Computer Scientist Should Know About Floating-Point Arithmetic by
Goldberg

### Numbers in Python

* Python has support for plain and long integers

* Plain integers are 32 or 64-bit signed integers depending on the platform

* Long integers have unlimited range

```
>>> 52**100
3984137914278306537107946300187788156651883090392267368064424070371960737746
8098814309384465476477916379562105903885691732986504663858102457926577952139
61405107689148645376L
>>>
```
python automatically converts from int to long when number gets too large, so it doesn't overflow
"L" specifies a long integer, as incremented python allocates more memory for it

* Floating point numbers are double precision (64-bit)

numpy behaves like fixed width integers

## Performance comparison

sums integers from 0 to a million and time it

`code/summation.cpp`:

```c++
#include <iostream>
#include <ctime>

int main() {
  std::clock_t start = std::clock();
  int sum = 0;
  for(int n = 0; n < 1000000; n++) {
    sum++;
  }
  double duration = (std::clock()-start) / (double) CLOCKS_PER_SEC;

  std::cout << sum << std::endl;
  std::cout << "time = " << duration << std::endl;

  return 0;
}
```

`code/summation1.py`:

```
import time

t0 = time.time()
total = 0
for n in range(1000000):
    total += 1
t1 = time.time()
print(total)
print("time = {}".format(t1-t0))
```

Output:

```
$ python summation1.py
1000000
time = 0.17532491684
$

$ ./summation
1000000
time = 9.53674e-07
$
```
python is a lot slower than C++
can try using xrange to be faster in python

Compile the C++ code with: `$ g++ summation.cpp -o summation`

### Python is kind of slow

One of the main disadvantages of a higher level language is that, while
comparatively easy to program, it is typically slow compared to C/C++, Fortran,
or other lower level languages

![fig](fig/python-v-compiled.png)

C++ sits much closer to hardwares; C compilers will inspect code and optimize it

### Object overhead

![fig](fig/object-overhead.png)

everything in python is an object, and this creates lots of overhead

### Options

* Python is great for quick projects, prototyping new ideas, etc.

* What if you need better performance?

* One option is to completely rewrite your program in something like C/C++

### Python C API

* Python has a C API which allows the use of compiled modules

![fig](fig/python-c-interface.png)

* The actual implementation of `string.find()` can be viewed at:

http://svn.python.org/view/python/trunk/Objects/stringlib/fastsearch.h

### Python compiled modules

* Python code in a `.py` file is actually executed in a hybrid approach by a mix
of the interpreter and compiled modules that come with Python

![fig](fig/python-compiled-modules.png)


### Extension modules

* The same Python C API used by the developers of Python itself also allows
other programmers to develop and build their own compiled extension modules

* These modules extend the functionality of Python with high performance
implementations of common operations

* Other languages, such as C++ and Fortran, are also supported by using the C
API

### NumPy, SciPy, matplotlib

use compiled code for fast tasks

* NumPy - multidimensional arrays and fundamental operations on them

* SciPy - Various math functionality (linear solvers, FFT, optimization, etc.)
utilizing NumPy arrays

* matplotlib - plotting and data visualization

* None of these packages seek to clone MATLAB, if you want that try something
like GNU Octave

### Python software stack
numpy is a bit like compiled modules

![fig](fig/python-stack.png)

### NumPy
use to represent arrays of number
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

### Arrays are not lists
only store a SINGLE type of data; FIXED TYPE; allows most efficient code and mem representation
we don't have append method to this array. you can concatenate it though
```py
>>> a[0] = "hello"
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
ValueError: invalid literal for long() with base 10: 'hello'
>>> a.append(8)
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
AttributeError: 'numpy.ndarray' object has no attribute 'append'
>>>
```

### NumPy arrays

* NumPy arrays contain **homogeneous** data

* Size is fixed, i.e. you can't append or remove

### Data types

* Integers

    * 8, 16, 32, and 64 bit signed and unsigned (numpy.int8, numpy.uint8, etc.)

* Floating point

    * 32, 64, 128 bit (numpy.float32, numpy.float64, etc.)

* Complex, strings, and Python object references also supported
      -but these cause overhead

Data is compactly stored, so we can quickly access next number in the array. Stores in a consecutive block of memory, so quicker

**contiguous memory**

C stores row major ordering
Fortran stores column major ordering

### Data type examples

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
array([-75,
7, 19], dtype=int8)
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
>>> a.shape
(2, 3)
>>> a.size
6
>>>
```
same as matlab, first index is column, next is rows
last index is contiguous in mem
### Internal representation

![fig](fig/numpy-representation.png)

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
>>>
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
[ 4.,
8., 17.]])
>>> numpy.savetxt('numbers2.txt', a)
>>>
```
savetxt dumps info to disk

### Remove single dimension entry

```py
>>> import numpy as np <-- usually standard to import as this
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
>>> b = numpy.squeeze(b)  <-- squeeze function gets rid of singleton dimensions
>>> b
array([0, 1, 2])
>>> b.shape
(3,)     <-- created a 1D numpy array, this is a python tuple with single element
>>> b = np.arrange(3).reshape(3,1)
>>> b.shape
(3,1)
>>> b.ndim   
2
```
array broadcasting: system with a bunch of rules (for example, outer product addition)
  -keep in mind in case you get unexpected behavior

### Array operations

```py
>>> a = numpy.arange(9, dtype=numpy.float64)
>>> a
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.])
>>> a[3:7] <--- can slice to get subarray
array([ 3.,  4.,  5.,  6.])
>>> a[3:7] = 0 <--- can assign to a subsequence (modify array)
>>> a
array([ 0.,  1.,  2.,  0.,  0.,  0.,  0.,  7.,  8.])
>>> 2*a
array([  0.,   2.,   4.,   0.,   0.,   0.,   0.,  14.,  16.])
>>> a*a
array([  0.,   1.,   4.,   0.,   0.,   0.,   0.,  49.,  64.])
>>> sum(a)
18.0
>>> min(a)
0.0
>>> max(a)
8.0
>>>
```
Slicing operation returns a ref to subarray rather than a subarray
```py
>>> b=a[3:7]
>>> b[0]=0
>>> b
array([0,4,5,6])
>>> a
array([0,1,2,3,4,5,6,7,8])

```
if we change b, it changes a

### Array operations

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
better for performance to use norm
### Speed of array operations

`code/summation2.py`:
```py
import numpy
import time

t0 = time.time()
total = sum(numpy.ones(1000000,dtype=numpy.int32))
t1 = time.time()
print(total)
print("time = {}".format(t1-t0))
```

Output:

```
$ python summation2.py
1000000
time = 0.428977012634
$
```

### Speed of array operations

`code/summation3.py`:

```py
import numpy
import time

t0 = time.time()
total = numpy.sum(numpy.ones(1000000,dtype=numpy.int32))
t1 = time.time()
print(total)
print("time = {}".format(t1-t0))
```

Output:

```
$ python summation3.py
1000000
time = 0.00641703605652
$
```
Conclusion: sum versus numpy.sum-> use numpy.sum to achieve best results (faster)

### Loops vs. array operations

* Loops you write in Python will be executed by the interpreter

* Some of the overloaded operators (e.g. `min`, `max`, `sum`, etc.) work albeit
  slowly


* Calling NumPy function or methods of the array object will invoke high
performance implementations of these operations

### Matrix operations

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
>>>
```

### array vs matrix

* NumPy has a dedicated matrix class

* However, the matrix class is not as widely used and there are subtle
differences between a 2D array and a matrix

* It is highly recommended that you use 2D arrays for maximum compatibility with
other NumPy functions, SciPy, matplotlib, etc.

* See here for more details:
<http://mathesaurus.sourceforge.net/matlab-numpy.html>
<http://www.scipy.org/NumPy_for_Matlab_Users>

(`array' or `matrix'? Which should I use?)

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

### Array slices and references

```py
>>> a = numpy.arange(9, dtype=numpy.float64)
>>> a
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.])
>>> b = a[2:7]
>>> b
array([ 2.,  3.,  4.,  5.,  6.])
>>> b[2] = -1
>>> b
array([ 2.,  3., -1.,  5.,  6.])
>>> a
array([ 0.,  1.,  2.,  3., -1.,  5.,  6.,  7.,  8.])
>>>
```

### Array copies

```py
>>> a = numpy.arange(9, dtype=numpy.float64)
>>> a
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.])
>>> b = a.copy()
>>> b
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.])
>>> b[4] = -1
>>> b
array([ 0.,  1.,  2.,  3., -1.,  5.,  6.,  7.,  8.])
>>> a
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.])
>>>
```

### Universal functions (ufuncs)
Need to call the numpy.sqrt() instead of the math module one

```py
>>> import numpy
>>> a = numpy.arange(9, dtype=numpy.float64)
>>> a
array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.])
>>> import math
>>> math.sqrt(a)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: only length-1 arrays can be converted to Python scalars
>>> numpy.sqrt(a)
array([ 0.        ,  1.        ,  1.41421356,  1.73205081,  2.        ,
        2.23606798,  2.44948974,  2.64575131,  2.82842712])
>>>
```

### Beyond just arrays


* NumPy has some support for some useful operations beyond the usual vector and
matrix operations:

    * Searching, sorting, and counting within arrays

    * FFT (Fast Fourier Transform)

    * Linear Algebra

    * Statistics

    * Polynomials

    * Random number generation

* SciPy has largely replaced much of this functionality,
plus added much more

### Warning

* Once you start making use of extension modules such as NumPy, SciPy, etc. the
chances of code "breaking" when you run it on different machines goes up
significantly

* If you do some of your development on machines other than corn (which isn't
the model we advise) you may run into issues

### Further Reading

* MATLAB users: <http://www.scipy.org/NumPy_for_Matlab_Users>
* NumPy tutorial at: <http://www.scipy.org/Tentative_NumPy_Tutorial>
* Official docs at: <http://docs.scipy.org/>
