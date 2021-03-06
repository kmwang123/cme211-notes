# CME 211: Lecture 10

Topics: Representation of numbers, Numpy overview

## Computer representation of data

* Computers represent and store everything in *binary* (base 2, 0s and 1s called binary digits or bits)

* Byte = 8 bits

### Simplified model of computer

![fig](lecture-10/fig/model-computer.png)

- Large sequence of bytes make up memory or DRAM.
- In diagram, each box = a single byte, each byte in memory has an address; usually 0x00 (hexadecimal, base 16)
- Fast computations done in registers
- Communication across memory bus is very slow compared to communication from register to alu
- nearby data is also brought along in memory bus, so it's ready to go in register

* temporal locality: likely i want data soon
* spatial locality: likely i want data nearby


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
- if we have 8 bits, then largest number is 2^7+2^6...+2^0 = 255, so we have a range from 0-255 or can represent 256.
- For negative number, use negative two's compliment

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

![lecture-10/fig/bits.png](lecture-10/fig/bits.png)

all floating point numbers take same amount of memory (same bits)
### Integer representation

* At the hardware level computers typically handle integers using 8, 16, 32, or
64 bits

![lecture-10/fig/dec-bin-table.png](lecture-10/fig/dec-bin-table.png)

bit shift to left = multiplying the number
bit shift to right = divide, but might lose remainder

### Integer range

* For `n` bits, there are only `2^n` unique combinations of 0s and 1s

* This limits the range of what can be represented with a fixed number of bits

### Sign bit

* Use one bit for sign and remaining bits for magnitude
cuts your positive values by a power of 2
two representations of 0 and -0, not good
causes hardware implementation to be more complicated

![lecture-10/fig/sign-bit.png](lecture-10/fig/sign-bit.png)

* Reduces the range of the magnitude from `2^n` to `2^(n-1)`

### Offset

* Apply an offset or bias to reinterpret the conversion between binary and
decimal

![lecture-10/fig/sign-offset.png](lecture-10/fig/sign-offset.png)

* Again, effectively reduces the range of the magnitude


### Unsigned integers

* Many programming languages support unsigned integers

* Python itself does not have unsigned integers, but Numerical Python (`numpy`) does

* Can use this to your advantage to expand the effective range available if
negative numbers don't need to be stored

### Overflow and underflow

* Attempting to assign a value greater/less than what can be represented by the data
type will result in overflow/underflow

* Overflow or underflow tend to cause wraparound, e.g. if adding together two
signed numbers causes overflow the result is likely to be a negative number

* Ex: overflow causes 128+1 = -127
* Ex: underflow causes -127-1 = 128

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

```
unsigned
2^8  = 256, so [0,  255]
2^16 = 65536, so [0,  65535]
2^32 = 4,294,967,296, so [0,  4,294,967,295]
2^64 = 18,446,744,073,709,551,616, so [0,  18,446,744,073,709,551,615]

signed
2^8 gives  [-128 to 127]
2^16 gives -2^15+1 to 2^15 [-32768 to 32767]
2^32 gives -2^31+1 to 2^31 [-2,147,483,648 to 2,147,483,647]
2^64 gives -2^63+1 to 2^63 ish (estimate)[-9.223372036854776e+18 to ?9.223372036854776e+18]

```

![lecture-10/fig/int-range.png](lecture-10/fig/int-range.png)

### Floating point representation

* How do I represent a floating point value using bits?

![lecture-10/fig/float.png](lecture-10/fig/float.png)
10-6 default, up to 6 accuracy

### Floating point standard

* IEEE (Institute of Electrical and Electronics Engineers) 754 is the technical
standard for floating point used by all modern processors

![fig](lecture-10/fig/float-table.png)

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

* Program sums integers from 0 to a million
* We time it and see that C++ is much faster
   - can try using xrange to be faster in python, but still slower

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

### Python is kind of slow

One of the main disadvantages of a higher level language is that, while
comparatively easy to program, it is typically slow compared to C/C++, Fortran,
or other lower level languages

![fig](lecture-10/fig/python-v-compiled.png)

* C++ sits much closer to hardwares
* C compilers will inspect code and optimize it

### Object overhead
* Everything in python is an object, which creates lots of overhead

![fig](lecture-10/fig/object-overhead.png)

### Options

* Python is great for quick projects, prototyping new ideas, etc.

* For better performance, rewrite program in C/C++

### Python C API

* Python has a C API which allows the use of compiled modules

![fig](lecture-10/fig/python-c-interface.png)

* The actual implementation of `string.find()` can be viewed at:

http://svn.python.org/view/python/trunk/Objects/stringlib/fastsearch.h

### Python compiled modules

* Python code in a `.py` file is actually executed in a hybrid approach by a mix
of the interpreter and compiled modules that come with Python

![fig](lecture-10/fig/python-compiled-modules.png)


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
* numpy is a bit like compiled modules

![fig](lecture-10/fig/python-stack.png)

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
* Only store a SINGLE type of data; FIXED TYPE; (homogenous)
* allows most efficient code and memory representation
* Size is fixed (no append()/remove())
    - No append method to this array, but can concatenate
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

same as matlab, first index is column, next is rows
last index is contiguous in mem
### Internal representation

![fig](lecture-10/fig/numpy-representation.png)

contiguous block of memory- data is together, so high performance

ions

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

# CME 211: Lecture 14

Friday, October 23, 2015

Topics:

* Introduction to C++
* Hello world
* Variables
* Strings


### C versus C++

* C is roughly a subset of C++

* C is a lower level language that has fewer abstractions over the hardware

* Even within C++ programs, the most computationally intense parts should be C
like for efficiency

* C is still used for many applications: Linux kernel, CPython interpreter, low
power or embedded systems, etc.

## A Simple C++ Program
* The OS runs a C++ program by calling main.
```c++
int main() //main has return type int and takes in empty list of parameters
{
  return 0; //block of statements in curly brace, return terminates the function
  // a nonzero return indicates what kind of error occurred
}
```
The value is returned to the shell and can be accessed using the command '$ echo $?'. Usually,
-1 means program failure.


## Hello world

In this lecture, we are going to start with a C++ source file and modify it to
show various things about C++.  If you desire to compile and execute subsequent
code block on your own, please modify the provided source (or start a new source
file).

Let's start with the file: `src/hello.cpp`
sometimes also .cc or .cxx or others. Some are standard so that C++ compiler will know what to do
if you pass in .c file, interpreter will compile that as standard C

```c++
#include <iostream> // input/output stream, these are like import in python

int main() // input arguments in main to put in command line arguments, int is the return type
{
  // everything in this code block is run
  /* Hello world program (this is a comment)
     this form of comment can span multiple line*/

  // this is also a comment, but only goes to the end of the line

  std::cout << "Hello world" << std::endl;
//std cout is the output string, we're sending it the string "hello world" and
//endl forces text to be displayed

  return 0; // Return value of the function. In this case, we return an "int"
}
```

### Compilation

* C++ programs have to be compiled
```
DN0a225536:src babagoatkw$ ls
hello.cpp	string.cpp	variables.cpp
DN0a225536:src babagoatkw$ vi hello.cpp
DN0a225536:src babagoatkw$ g++ hello.cpp <-- g++ is the compiler, creates an a.out file
DN0a225536:src babagoatkw$ ls
a.out		hello.cpp	string.cpp	variables.cpp
DN0a225536:src babagoatkw$ ./a.out  <-- run using the ./ command (want to run an executable located in this directory)
Hello world
```

Note: use "cd -" to go back to original directory you were in

* Compilation is the process of translating the human readable source code into
an executable containing the machine instructions that the computer will use
while the program is running

* Although C++ source code can be made portable and compiled on multiple
machines (Linux, Windows, Mac) the executables are specific to an operating
system and underlying processor

```
DN0a225536:src babagoatkw$ g++ hello.cpp -o hello #can name the compiled code to something different
DN0a225536:src babagoatkw$ ls
a.out		hello		hello.cpp	string.cpp	variables.cpp
DN0a225536:src babagoatkw$ ./hello
Hello world
```

### Compiling on corn (use g++ for GNU's)

```
[nwh@icme-nwh src] $ ls
hello.cpp
[nwh@icme-nwh src] $ g++ -Wall -Wconversion -Wextra hello.cpp
[nwh@icme-nwh src] $ ls
a.out  hello.cpp
```

Details:

* `g++`: GNU C++ compiler program

* `-Wall`, `-Wconversion`, `-Wextra` are flags to enable all warnings
 - "-W" tells compiler to give us warnings (they are flags). If we are compiling with these flags and it's producing warnings, fix those warnings
* `hello.cpp` is the name of the C++ source file to compile

### Running Hello world

```
[nwh@icme-nwh src] $ ls
a.out  hello.cpp
[nwh@icme-nwh src] $ ./a.out
Hello world
```

An explanation for the dot slash: <http://www.linfo.org/dot_slash.html>

Why `a.out`?: <https://en.wikipedia.org/wiki/A.out>
- is just the default

### Naming your executable

Specify the output executable name:

```
[nwh@icme-nwh src] $ g++ -Wall -Wconversion -Wextra hello.cpp -o hello
[nwh@icme-nwh src] $ ls
a.out  hello  hello.cpp
[nwh@icme-nwh src] $ ./hello
Hello world
```

### Streams

* Standard C++ uses "streams" for output

* Stream: sequence of characters read from or written to an IO device; characters are gnerated or consumed sequentially over time

* The  `iostream` library have two types:  `istream` and `ostream`, which represent standard input and output streams
    - For input, use `istream` object named `cin`
    - For output, use `ostream` object named `cout`
    - Library also has `ostream` objects named `cerr` (for standard error for warning) and `clog`  (for general info about execution of the program)

* Example: In this context, we keep passing strings (and
other identifiers) to the output stream, which is then sent to the terminal.
There are other forms of streams as well.

```
std::cout << "Hello world" << std::endl;
```

* `cout` is in the `std` namespace and refers to the standard output (stdout)
  stream

* `endl` is in the `std` namespace and inserts '\n' and flushes the stream

* `<<` is the stream insertion operator

### `std::endl`

* Inserts a newline character (`\n`) and flushes the buffer

* You can also put in a newline yourself, and let the buffer flush automatically
as necessary

```c++
#include <iostream>

int main() {
  int a;
  std::cout << a << std::endl; // with flags, will get warning that we haven't initialized a. Without flags, a=0. Compiler can also give warnings if there is an unused variable in the code. Warnings don't stop the compilation problems- they just spit out warnings

  std::cout << "Hello world\n"; // this will not flush the buffer. It's good to use standard endl for outputting new lines
  return 0;
}
```

```
kmwang14@corn29:~/CME211/Cplusplus$ g++ -std=c++11 -Wall -Wconversion -Wextra helloWarning.cpp
helloWarning.cpp: In function 'int main()':
helloWarning.cpp:5:16: warning: 'a' is used uninitialized in this function [-Wuninitialized]
   std::cout << a << std::endl;
```

## Include (header) files

* When  we do  `#include` that  is somewhat  analogous to  an import  in Python,
giving us access to functionality defined in another file

* In C++ the access to even fundamental functionality like outputting to the
screen requires specifying the proper include file(s)

* Include files in C++ work a bit differently when it comes to namespaces

* However, namespaces in C++ still generally serve the same purpose as
namespaces in Python

### Namespaces

* In Python the name of the namespace comes from the file name, and everything
in the file is automatically in that one namespace

* A C++ include file might contain functions, classes, etc. that are not in a
namespace at all

* An include file could also contain functions, classes, etc. from multiple namespaces

* Namespaces can also span multiple include files, like for the C++ standard library

### C++ Standard Library

* The C++ Standard Library is all the built in functionality that is part of the
C++ language

* Namespace for this library is `std`

* `iostream` contains `cout` in the `std` namespace

* By default, when using `cout`, we need to specify the namespace and fully qualify the symbol as `std::cout`

### Scope resolution operator

* The `::` is called the scope resolution operator
  - for example, `std::out`, so we need  `namespace::what we want`

* Used to indicate what namespace something comes from

* If a namespace is required that will typically be listed in the documentation,
or by inspecting the include file

* Will talk about namespaces more when we start writing our own include files

### Another namespace option

```cpp
#include <iostream>

// this is not considered good practice
using namespace std;

int main() {
  cout << "Hello world" << endl;
  return 0;
}
```

```
$ g++ -Wall -Wconversion -Wextra hello.cpp -o hello
$ ./hello
Hello world
$
```

### Another namespace option

```cpp
#include <iostream>

// good practice, (but not when you write a header file!)
using std::cout;
using std::endl;

int main() {
  cout << "Hello world" << endl;
  return 0;
}
```
### C++ types
Rules of thumb (pg. 63)
- Usually use int for integer arithmetic. Short is usually too small and in practice, long often has same size as int. If your data values are larger than the min size of an int, use long
- Don't use plain char or bool in arithmetic expressions (use them to hold characters or truth values)
- Use double for floating-point computation; float usu doesn't have enough precision, and cost of double-precision calculations versus single-precision is negligible
![MidtermRev/cppTypes.png](MidtermRev/cppTypes.png)
```cpp
unsigned char c = -1; // assuming 8-bit chars, c has value 255
bool b = 42; // b is true
int i = b; // i has value 1

### Blocks of code

* Blocks of code, such as the code comprising a function, conditional, loop,
etc. are indicated by enclosing them in curly brackets

* There are very few places where whitespace matters to the compiler

```cpp
#include <iostream>
int main(){std::cout<<"Hello world"<<std::endl;return 0;}
```

```
$ g++ -Wall -Wconversion -Wextra hello.cpp -o hello
$ ./hello5
Hello world
$
```

### Return value from `main()`

```cpp
#include <iostream>

int main() {
  /* Hello world program*/
  std::cout << "Hello world" << std::endl;
  return 7;
}
```
* returning a positive number represents an error
```
[nwh@icme-nwh src] $ g++ -Wall -Wconversion -Wextra hello.cpp -o hello
[nwh@icme-nwh src] $ ./hello
Hello world
[nwh@icme-nwh src] $ echo $?
7
[nwh@icme-nwh src] $ ls
a.out  hello  hello.cpp
[nwh@icme-nwh src] $ echo $?
0
```

* Unix standard: programs return `0` under normal conditions and other numbers
  on error conditions

### Initializing variables

```cpp
#include <iostream>

int main() {
  int a = 2;
  int b;

  b = 3;
  int c = a + b;

  std::cout << "c = " << c << std::endl;

  return 0;
}
```

```
[nwh@icme-nwh src] $ g++ -Wall -Wconversion -Wextra variables.cpp -o variables
[nwh@icme-nwh src] $ ./variables
c = 5
```


### Mixed number types

* manually cast data to the right type if you need to; best thing to do is round, then cast it

```cpp
#include <iostream>

int main() {
  int a, b;

  a = 2.7;
  b = 3;
  int c = a + b;

  std::cout << "c = " << c << std::endl;

  return 0;
}
```

```
[nwh@icme-nwh src] $ g++ -Wall -Wconversion -Wextra variables.cpp -o variables
variables.cpp: In function ‘int main()’:
variables.cpp:6:5: warning: conversion to ‘int’ alters ‘double’ constant value [-Wfloat-conversion]
   a = 2.7;
     ^
```

### Mixed number types

```cpp
#include <iostream>

int main() {
  int a, b;

  a = (int)2.7; // int(2.7) would also work

  b = 3;
  int c = a + b;

  std::cout << "c = " << c << std::endl;

  return 0;
}
```

```
[nwh@icme-nwh src] $ g++ -Wall -Wconversion -Wextra variables.cpp -o variables
[nwh@icme-nwh src] $ ./variables
c = 5
```

### Double precision floating point
* Double has 2x the precision of float (64 bits instead of 32)

```cpp
#include <iostream>

int main() {
  int a;
  double b = 3.14;

  a = 2;
  double c = a*b;

  std::cout << "c = " << c << std::endl;

  return 0;
}
```
```
$ g++ -Wall -Wconversion -Wextra variables.cpp -o variables
$ ./variables6
c = 6.28
$
```

### Rounding

```cpp
#include <iostream>
#include <cmath> // include this to use round() function

int main() {
  int a;
  double c = 2.7;

  a = (int)round(c);
  // note that the round() function
  // is not in the std namespace

  std::cout << "a = " << a << std::endl;

  return 0;
}
```

```
$ g++ -Wall -Wconversion -Wextra variables.cpp -o variables
$ ./variables
a = 3
$
```

### Key data types

* C++ has all of the data types that we talked about when we looked at computer
representation of data in conjunction with NumPy

  * Various signed and unsigned integers

  * Floating point (float, double, long double)

* A Boolean data type

* A string object

### Boolean (true/false values)

```cpp
#include <iostream>

int main() {
  bool equal;

  equal = 2 == 3; // assigning the bool to equal. Should be equal = false
  std::cout << equal << std::endl;

  equal = true; // All lowercase

  std::cout << equal << std::endl;

  return 0;
}
```

```
$ g++ -Wall -Wconversion -Wextra boolean.cpp -o boolean
$ ./boolean
0
1
$
```

## Strings

* There are two options for strings in C++
 - we'll mainly use string rather than char arrays

* An array of characters (same as C)

* A string object

* The latter is much safer and easier to use

### String example

```cpp
#include <iostream>
#include <string> // need to include this

int main() {
  std::string hello = "Hello world"; // pass as a standard string

  std::cout << hello << std::endl;

  return 0;
}
```

```
$ g++ -Wall -Wconversion -Wextra string.cpp -o string
$ ./string
Hello world
$
```
### String initialization
```cpp
string s1; // default initialization; s1 is empty string
string s2 = s1; // s2 is copy of s1
string s3 = "hiya"; // s3 is copy of string literal
string s4(10, 'c'); // s4 is cccccccccc
```

### String concatenation

```cpp
#include <iostream>
#include <string>

int main() {
  std::string hello = "Hello";
  std::string world = " world";

  std::string helloworld = hello + world; // string concatenation

  std::cout << helloworld << std::endl;

  return 0;
}
```

```
$ g++ -Wall -Wconversion -Wextra string.cpp -o string
$ ./string
Hello world
$
```

### String finding

```cpp
#include <iostream>
#include <string>

int main() {
  std::string hello = "Hello";
  std::string lo = "lo";

  std::cout << hello.find(lo) << std::endl; //first index found

  return 0;
}
```

```
$ g++ -Wall -Wconversion -Wextra string.cpp -o string
$ ./string
3
$
```

### String functions
Pg. 128
string.empty() is useful
![fig](MidtermRev/strings.png)

### Compiler flags
* -Wall: Enable "all" warnings
* -o: specify name of output (executable) file
* -g: include debugging information for debugger or Valgrind
* -std=c++11: Enable use of C++ 2011 Standard
* -03: Do extensive optimizations (slower compilation, faster execution)

## Required reading

* **C++ Primer, Fifth Edition** by Lippman et al.

* Available on Safari ProQuest:
  <http://proquest.safaribooksonline.com/book/programming/cplusplus/9780133053043>

* Chapter 1: Getting Started, Sections 1.1 - 1.3

* Chapter 2: Variables and Basic Types, Sections 2.1 - 2.2

* Chapter 3: Strings, Vectors, and Arrays: Sections 3.1 - 3.2

## Resources

* <http://www.cppreference.com>

* <http://www.cplusplus.com>

* <http://www.linfo.org/index.html>

# CME 211: Lecture 15

Monday, October 26, 2015

Topics:

* static arrays
* variable scope
* looping

Coliru.stacked-crooked.com is a handy website that you can write C++ code in and compile.

## Static arrays

The size (length) of static arrays is known at compile time.  See `src/array.cpp`:

```c++
#include <iostream>

int main() {
  int a[3]; //array with 3 elements

  a[0] = 0;
  a[1] = a[0] + 1;
  a[2] = a[1] + 1;

  std::cout << "a[0] = " << a[0] << std::endl;
  std::cout << "a[1] = " << a[1] << std::endl;
  std::cout << "a[2] = " << a[2] << std::endl;

  return 0;
}
```

Compile and look at the output:

```
$ g++ -Wall -Wconversion -Wextra array.cpp -o array
$ g++ -Wall -Wconversion -Wextra array.cpp -o array
$ ./array
a[0] = 0
a[1] = 1
a[2] = 2
$
```

### Out of bounds access

Accessing static arrays (or any array for that matter) out of bounds leads to
undefined behavior and is a particularly nasty problem.  Modify `src/array.cpp`
to the following:

```cpp
#include <iostream>

int main() {
  int a[3]; // Array has 3 elements


  a[0] = 0;
  a[1] = a[0] + 1;
  a[2] = a[1] + 1;
  a[3] = a[2] + 1; // Out of bounds access


  std::cout << "a[0] = " << a[0] << std::endl;
  std::cout << "a[1] = " << a[1] << std::endl;
  std::cout << "a[2] = " << a[2] << std::endl;
  std::cout << "a[3] = " << a[3] << std::endl;

  return 0;
}
```

Now, compile and run:

```
$ g++ -Wall -Wconversion -Wextra array.cpp -o array
$ ./array
a[0] = 0
a[1] = 1
a[2] = 2
a[3] = 3
$
```

Nothing bad happened here.  But, the behavior is undefined. We're overwriting memory in the computer that is used for something else, but we don't know what that is, so this is very very bad.

Python checks this for your (out of bounds error), but C++ doesn't! Leads to all sorts of problems

### Address Sanitizer

We can instrument the executable to detect out of bound memory access in static
arrays.  To do this we enable the "address sanitizer" at compile time.

* <https://code.google.com/p/address-sanitizer/>

* Incorporated into GNU (and other) compilers

* Adds instrumentation around memory accesses

* Enabled at compile time

* Program will use more memory and run slower

Let's enable this with `g++`:
Backslash '\' tells command line to keep reading on the next line
```

$ g++ -Wall -Wconversion -Wextra \
    -g \ // enables debugging signal
    -fsanitize=address \
    array.cpp -o array
$
```

Notes:

* The `-g` flag adds debugging symbols to the output executable

* The `-fsanitize=address` enables the address sanitizer

* In `bash` the `\` character allows line continuation

### Address Sanitizer and `gdb`

We can use the GNU debugger `gdb` to get more precise information about the
error

## Multidimensional static arrays

See `src/md_array.cpp`:

```c++
#include <iostream>

int main() {
  // declare a 2D array
  int a[2][2]; // accessing data along the second element or rightmost index is CONTIGUOUS in memory (this is C-style format)

  a[0][0] = 0;
  a[1][0] = 1;
  a[0][1] = 2;
  a[1][1] = 3;

  std::cout << "a = " << a << std::endl; //prints the memory address of a

  std::cout << "a[0][0] = " << a[0][0] << std::endl;
  std::cout << "a[1][0] = " << a[1][0] << std::endl;
  std::cout << "a[0][1] = " << a[0][1] << std::endl;
  std::cout << "a[1][1] = " << a[1][1] << std::endl;

  return 0;
}
```

Compile and run:

```
$ g++ -Wall -Wconversion -Wextra md_array.cpp -o md_array
$ ./md_array
a = 0x7fffe2a9e8d0
a[0][0] = 0
a[1][0] = 1
a[0][1] = 2
a[1][1] = 3
$
```

### Array operations

You can't do assignment with C++ static arrays.  Let's modify `src/md_array.cpp`:

```c++
#include <iostream>

int main() {
  // declare a 2D array
  int a[2][2];

  // declare another 2D array
  int b[2][2];

  b = a; // cannot assign a to b, gives compile error. If you want to copy array, you need to do a double nested for loop and do it yourself

  a[0][0] = 0;
  a[1][0] = 1;
  a[0][1] = 2;
  a[1][1] = 3;

  std::cout << "a = " << a << std::endl;
  std::cout << "b = " << b << std::endl;

  std::cout << "a[0][0] = " << a[0][0] << std::endl;
  std::cout << "a[1][0] = " << a[1][0] << std::endl;
  std::cout << "a[0][1] = " << a[0][1] << std::endl;
  std::cout << "a[1][1] = " << a[1][1] << std::endl;

  return 0;
}
```

Attempt to compile:

```
$ g++ -Wall -Wconversion -Wextra md_array.cpp -o md_array
md_array.cpp: In function 'int main()':
md_array.cpp:10:5: error: invalid array assignment
   b = a;
     ^
$
```

## Scope

* A variable declared within a block is only accessible from within that block

* Blocks are denoted by curly brackets, typically the same brackets that denote
a function, loop or conditional body, etc.

* Sub-blocks can declare different variables that have the same name as
variables at broader scope

* Variables should not be declared with excessive scope

### Scope examples

```cpp
#include <iostream>

int main() {
  {
    int n = 5;
  }

  std::cout << "n = " << n << std::endl; //compile time error since n is outside the scope and we are trying to print it

  return 0;
}
```

Output:

```
$ g++ -Wall -Wconversion -Wextra scope.cpp -o scope
scope.cpp: In function 'int main()':
scope.cpp:5:9: warning: unused variable 'n' [-Wunused-variable]
     int n = 5;
         ^
scope.cpp:8:26: error: 'n' was not declared in this scope
   std::cout << "n = " << n << std::endl;
                          ^
$
```

### Scope examples

```cpp
#include <iostream>
#include <string>

int main() {
  std::string n = "Hi";
  std::cout << "n = " << n << std::endl;

  {
    int n = 5;
    {
      std::cout << "n = " << n << std::endl;
    }
  }

  return 0;
}
```
This bad practice since you don't want to rename variables in a subscope
```
$ g++ -Wall -Wconversion -Wextra scope.cpp -o scope
$ ./scope
n = Hi
n = 5
$
```
If you comment out the int n=5 block, get:
```
$ g++ -Wall -Wconversion -Wextra scope.cpp -o scope
$ ./scope
n = Hi
n = Hi <- this is the print statement called in the nested for loop
$
```
## C++ for loop

Start with an example.  See `src/for_loop1.cpp`:

```cpp
#include <iostream>

int main() {
  for (int i = 0; i < 10; ++i) {
    std::cout << "i = " << i << std::endl;
  }
  return 0;
}
```

Compile and run:

```
$ g++ -Wall -Wconversion -Wextra for_loop1.cpp -o for_loop1
$ ./for_loop1
i = 0
i = 1
i = 2
i = 3
i = 4
i = 5
i = 6
i = 7
i = 8
i = 9
$
```

### Anatomy of a for loop

```cpp
for (expression1; expression2; expression3) {
  // loop body
}
```

* `expression1`: evaluated once at the start of the loop
* `expression2`: conditional statement evaluated at the start of each loop iteration,
  terminate if conditional statement returns false
* `expression3`: evaluated at the end of each iteration

### Another `for` loop example

File `src/for_loop2.cpp`:

```cpp
#include <iostream>

int main() {
  int n, sum;

  sum = 0;
  for (n = 0; n < 101; ++n) {
    sum += n;
  }

  std::cout << "sum = " << sum << std::endl;
  return 0;
}
```

Output:

```
$ g++ -Wall -Wconversion -Wextra for_loop2.cpp -o for_loop2
$ ./for_loop2
sum = 5050
```

### Increment and decrement

* Increment (`++`) and decrement (`--`) are just shorthand for incrementing or
  decrementing by one

* You can put them before or after a variable

* See `src/increment.cpp`

```cpp
#include <iostream>

int main() {
  int n = 2;

  std::cout << "n = " << n << std::endl;
  n++;
  std::cout << "n = " << n << std::endl;
  ++n;
  std::cout << "n = " << n << std::endl;
  n--;
  std::cout << "n = " << n << std::endl;
  --n;
  std::cout << "n = " << n << std::endl;

  return 0;
}
```

Output:

```
$ g++ -Wall -Wconversion -Wextra increment.cpp -o increment
$ ./increment
n = 2
n = 3
n = 4
n = 3
n = 2
```

### Prefix (`++n`) vs. postfix (`n++`) increment operators

* The postfix operator creates a temporary and returns the value before
  incrementing

* The prefix operator returns a reference after incrementing

Example (`src/increment2.cpp`):

```cpp
Pseudocode:
a=1
print(a)
print(return a++)
print(a)

a=1
print(a)
print(return ++a)
print(a)

```

Output:

```
a: 1
Test postfix: //copy value of a, store it, then increment
return of a++: 1 //return original value
a: 2
Now test prefix: //increment first, then store and returns
return of ++a: 2 //return incremented value
a: 2
```
* Postfix is more expensive

### Iterating through an array

`src/for_loop3.cpp`:

```cpp
#include <iostream>

int main() {
  int n = 5;
  double a[16];

  /* Initialize a to zeros. */

  for (int n = 0; n < 16; n++) {
    a[n] = 0.;
  }

  std::cout << "a[0] = " << a[0] << std::endl;
  std::cout << "n = " << n << std::endl;

  return 0;
}
```

```
$ g++ -Wall -Wconversion -Wextra for_loop3.cpp -o for_loop3
$ ./for_loop3
a[0] = 0
n = 5
```

### Variations on for loop

```
#include <iostream>

int main() {
  int n = 0, sum = 0;
  // here n is declared with excessive scope
  // n is not needed outside of the for loop
  for (; n <= 100;) {
    sum += n;
    n++;
  }
  std::cout << "sum = " << sum << std::endl;

  return 0;
}
```

### Variations on for loop

```cpp
#include <iostream>

int main() {
  int sum = 0;
  // n may be declared in the first for loop expression
  for (int n = 0; n <= 100;) {
    sum += n;
    n++;
  }
  std::cout << "sum = " << sum << std::endl;

  return 0;
}
```

### Infinite loops

No termination condition given

See `src/inf_loop.cpp`:

```cpp
#include <iostream>

int main() {
  for (;;) {
  }

  return 0;
}
```

```
$ ./inf_loop
```

* Can generally be terminated with `Ctrl-c`

* If that doesn't work use `Ctrl-z` to background and then kill that job number

### `for` loop brackets

```cpp
#include <iostream>

int main() {
  int sum = 0;

  for (int n = 0; n < 101; n++)
    sum += n; // One statement loop body, does not have to be enclosed

  std::cout << "sum = " << sum << std::endl;

  return 0;
}
```

### Common mistake

```cpp
#include <iostream>

int main() {
  int n, sum, product;

  sum = 0;
  product = 1;
  for (n = 1; n < 11; n++)
    sum += n;
    product *= n; // Not part of for loop


  std::cout << "sum = " << sum << std::endl;
  std::cout << "product = " << product << std::endl;

  return 0;
}
```
Output:
```cpp
sum = 55
product = 11 //this wasn't included in for loops

```

### Common mistake
```cpp
#include <iostream>

int main() {
  int n;

  int sum = 0;
  for (n = 0; n < 101; n++); // no loop body since there's a semicolon here
  {
    sum += n;
  }

  std::cout << "sum = " << sum << std::endl;

  return 0;
}
```
Output:

```cpp
sum = 101 (since n gets incremented all the way to 101 before we do sum += n)

```


### Nested loops example

```cpp
#include <iostream>

int main() {
  double a[3][3];

  /* Initialize a to zeros. */

  for (int n = 0, i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
      a[i][j] = n;
      n++;
    }
  }

  /* Print a. */

  for (int i = 0; i < 3; i++) {
    std::cout << a[i][0];
    for (int j = 1; j < 3; j++) {
      std::cout << " " << a[i][j];
    }
    std::cout << std::endl;
  }

  return 0;
}
```

### Read in unknown number of inputs using for loop

```cpp
#include <iostream>
int main() {
  int sum = 0, value = 0;
  while (std::cin >> value)
      sum += value;
  std::cout << "Sum is: " << sum << std::endl;
  return0;
}

```
Output:
```
Sum is: 18
```

### `while` loop

```cpp
#include <iostream>

int main() {
  int n = 0, sum = 0;
  while (n <= 100) {
    sum += n;
    n++;
  }
  std::cout << "sum = " << sum << std::endl;

  return 0;
}
```
Output:
```
sum = 5050
```
### Common mistake

* This gets stuck in an infinite loop since n will always be <= 100 since n is not being incremented

```cpp
#include <iostream>

int main() {
  int n = 0, sum = 0;

  while (n <= 100); // no loop body
  {
    sum += n;
    n++;
  }
  std::cout << "sum = " << sum << std::endl;

  return 0;
}
```


### `do`-`while` loop

* A while loop may execute zero times if the conditional is not true on initial
evaluation

* C/C++ has a do-while loop that is very similar to a while loop, but always
executes at least once

```cpp
do {
  // loop body
} while (expression);
```

Note the semicolon at the very end!

### Reading

* **C++ Primer, Fifth Edition** by Lippman et al.

* <http://proquest.safaribooksonline.com/book/programming/cplusplus/9780133053043>

* Chapter 1: Getting Started, Sections 1.4.1 and 1.4.2 (while and for)

* Chapter 3: Strings, Vectors, and Arrays: Sections 3.5 and 3.6 (arrays)

# CME 211: Lecture 16

Wednesday, October 27, 2015

Topics:

* conditionals
* basic file operations in C++

## Conditional statements in C++

C++ has three conditional statements:

* `if`

* `switch`

* C++ ternary operator: `(x == y) ? a : b`

## C++ `if`

```cpp
#include <iostream>

int main() {
  int n = 2;

  std::cout << "n = " << n << std::endl;
  if (n > 0) {
    std::cout << "n is positive" << std::endl;
  }

  return 0;
}
```

Output:

```
$ ./if1
n = 2
n is positive
$
```

Note: brackets `{...}` are not needed for a single line `if` block.  However, I recommend always putting them in.

### `else if`

```cpp
#include <iostream>

int main() {
  int n = -3;

  std::cout << "n = " << n << std::endl;

  if (n > 0) {
    std::cout << "n is positive" << std::endl;
  }
  else if (n < 0) {
    std::cout << "n is negative" << std::endl;
  }

  return 0;
}
```

```
$ ./if2
n = -3
n is negative
$
```

### `else`
This is executed if none of the other blocks are executed (last case)
```cpp
#include <iostream>

int main() {
  int n = 0;

  std::cout << "n = " << n << std::endl;

  if (n > 0) {
    std::cout << "n is positive" << std::endl;
  }
  else if (n < 0) {
    std::cout << "n is negative" << std::endl;
  }
  else {
    std::cout << "n is zero" << std::endl;
  }

  return 0;
}
```

```
$ ./if3
n = 0
n is zero
$
```
### Read in numbers and print out occurence
input is 42 42 42 42 42 55 55 62 100 100 100
This only works if numbers are in order (can use algorithm to sort)
output should be:
42 occurs 5 times
55 occurs 2 times
62 occurs 1 times
100 occurs 3 times

```cpp
#include <iostream>
int main() {
  int currVal = 0, val = 0;
  if (std::cin >> currVal) {
    int cnt = 1; //store count for current value we're processing
    while (std::cin >> val) {
       if (val == currVal)
          ++cnt;
       else {
          std::cout << currVal << " occurs "
                    << cnt << " times" << std::endl;
          currVal = val;
          cnt = 1;
       }
    } //end while loop
    //print count for the last value in file
    std::cout << currVal << " occurs "
              << cnt << " times" << std::endl;
  }
  return 0;
}
```
### Common mistakes

* Empty `if` due to extraneous semi-colon
* Assignment in the conditional expression. This gives an compiler error if assigned for a conditional. If no error, code may not run properly
```cpp
if (n = 0) //instead of if (n == 0)
  std::cout << "n is zero" << std::endl;
```
Can also switch order of variable to force noncompile (ex: if(0 == n)), called a yoda condition
Note: some people recommend always putting the 'literal' before the variable.
This is known as a
[Yoda Condition](https://en.wikipedia.org/wiki/Yoda_conditions).

### `break`

The `break` keyword breaks out of the current loop.

```cpp
#include <iostream>

int main() {

  for (unsigned int n = 0; n < 10; n++) {
    std::cout << n << std::endl;
    if (n > 3) break;
  }

  return 0;
}
```

```
$ ./break
0
1
2
3
4
$
```

### `continue`

The `continue` keyword moves to the next loop iteration.

```
#include <iostream>

int main() {
  for (unsigned int n = 0; n < 10; n++) {
    if (n < 7) continue;
    std::cout << n << std::endl;
  }

  return 0;
}
```

```
$ ./continue
7
8
9
$
```

### Logical operators

* C++ has two choices for logical operators

  * Newer style `and`, `or`, `not`

  * Older style `&&`, `||`,'!'

* Latter are backwards compatible with C

### Logical AND

```cpp
#include <iostream>

int main() {
  int a = 7;
  int b = 42;

  // the following are equivalent

  if (a == 7 and b == 42)
    std::cout << "a == 7 and b == 42 is true" << std::endl;

  if (a == 7 && b == 42)
    std::cout << "a == 7 && b == 42 is true" << std::endl;

  return 0;
}
```

```
$ ./logical1
a == 7 and b == 42 is true
a == 7 && b == 42 is true
$
```

### `0` is false, everything else is true
use cpp reference to see how to print format with precision

```cpp
#include <iostream>

int main() {
  int a[] = {-1, 0, 1, 2};

  for (int n = 0; n < 4; n++) {
    if (a[n])
      std::cout << a[n] << " is true" << std::endl;
    else
      std::cout << a[n] << " is false" << std::endl;
  }

  return 0;
}
```

```
$ ./logical2
-1 is true
0 is false
1 is true
2 is true
$
```

### Bitwise results
common mistake: doing bitwise and instead of logical

```cpp
#include <iostream>

int main() {
  int a = 1;
  int b = 2;

  if (a)
    std::cout << "a is true" << std::endl;
  else
    std::cout << "a is false" << std::endl;

  if (b)
    std::cout << "b is true" << std::endl;
  else
    std::cout << "b is false" << std::endl;

  if (a & b) //this is a bitwise comparison
    std::cout << "a & b is true" << std::endl;
  else
    std::cout << "a & b is false" << std::endl;

  return 0;
}
```

Bitwise and of 1 (01) and 2 (10) becomes (00), so it becomes false
```
$ g++ -Wall -Wconversion -Wextra logical3.cpp -o logical3
$ ./logical3
a is true
b is true
a & b is false
$
```

### `switch`

* `if`, `else if`, `else`, etc. gets verbose if you have many paths of execution

* Can use a switch statement instead:

- DONT FORGET to add BREAK! Otherwise other cases will be executed
- default is executed if choice not equal to any other cases

```cpp
switch (choice) {
  case `C': clearRecord(); break;
  case `D': deleteRecord(); break;
  case `A': addRecord(); break;
  case `P': printRecord(); break;
  default: std::cout << "Bad choice\n";
}
```

### `switch` and `enum` example
enum is a new type that has text values; makes code more readable
might have enum for days of the week, months, anything that we often think of enumerated with text (not numbers)

```cpp
enum direction {
  left,
  right,
  up,
  down
};

//Can also do this and assign a keyword to some integer:
// can also define inside of a function
enum direction {
  left=100,
  right=123,
  up=32,
  down=88
};

int main() {
  direction d = right;

  std::string txt = "you are going ";
  switch (d) {
    case left:
      txt += "left"; break;
    case right:
      txt += "right"; break;
    case up:
      txt += "up"; break;
    case down:
      txt += "down"; break;
  }
  std::cout << txt << std::endl;
  return 0;
}
```

```
$ ./switch1
you are going right
$
```


- Compiler warnings will tell you if you are missing some cases.

### Ternary operator

This is called the "ternary" operator:

-b returned if b<0 is true
b returned if b<0 is false

```
a = b < 0 ? -b : b;
```

Equivalent code:

```
if (b < 0)
  a = -b;
else
  a = b;
```

Anatomy:

```
[conditional] ? [return expression if true] : [return expression if false];
```

### `goto`

"If you find yourself using a `goto` statement within a program, then you have not thought about the problem and its implementation for long enough"

### C++ file I/O

* Like outputting to the screen, file I/O is also handled via streams

* Three stream options:

  * `ofstream`: output file stream (i.e. write)

  * `ifstream`: input file stream (i.e. read)

  * `fstream`: file stream (i.e. read or write)

### Writing to file using `ofstream`

Code:

```cpp
#include <iostream>
#include <fstream>
#include <string>

int main() {
  std::string filename = "file.txt";

  std::ofstream f; //use ofstream for output
  f.open(filename);
  if (f.is_open()) { //test to make sure file is open
    f << "Hello" << std::endl;
    f.close();
  }
  else {
    std::cout << "Failed to open file" << std::endl;
  }

  return 0;
}
```

## Writing an array of values

```cpp
#include <iostream>
#include <string>
#include <fstream>
//  Define constants to size the static array
#define ni 2  // anytime you see a "ni", replace it with a 2
#define nj 3  // anytime you see a "nj", replace it with a 3

int main() {
  int a[ni][nj];
  int n = 0;
  //Initialize the array values to 0, 1, 2...
  for (int i = 0; i < ni; i++) {
    for (int j = 0; j < nj; j++) {
      a[i][j] = n;
      n++;
    }
  }

  // Store the array values in a file
  std::string filename = "array.txt";
  std::ofstream f;
  f.open(filename);
  if (f.is_open()) {
    f << ni << " " << nj << std::endl;
      for (int i = 0; i < ni; i++) {
        f << a[i][0];
        for (int j = 1; j < nj; j++) {
          f << " " << a[i][j];
        }
        f << std::endl;
      }
    f.close();
  }
  return 0;
}
```

### Reading from a file

* Not as easy or convenient as in Python

* We will start by looking at how to read the simple array file we previously
wrote

### Reading files using `ifstream`

```
#include <fstream>
#include <iostream>

int main() {
  std::ifstream f;
  f.open("array.txt");
  int val;
  while (f >> val) {
    std::cout << val << std::endl;
  }
  f.close();

  return 0;
}
```

Output:

```
$ g++ -std=c++11 -Wall -Wconversion -Wextra ifstream1.cpp -o ifstream1
$ ./ifstream1
2
3
0
1
2
3
4
5
$
```

### Reading into an array

```cpp
#include <fstream>
#include <iostream>

int main() {
  std::ifstream f;
  f.open("array.txt");
  int nx, ny;
  f >> nx >> ny; //read in size of array

  int a[nx][ny];
  for (int i = 0; i < nx; i++) {
    for (int j = 0; j < ny; j++) {
       f >> a[i][j];
    }
  }
  f.close();

  //print out values in array
  for (int i = 0; i < nx; i++) {
    for (int j = 0; j < ny; j++) {
       std::cout << "a[" << i << "]"
                 << "[" <<j<<"] = " <<  a[i][j] << std::endl;
    }
  }

  return 0;

}
```
Inputfile Looks like:
```
kmwang14@corn21:~/CME211/Cplusplus/lecture-17$ cat array.txt
2 3
0 1 2
3 4 5
```
Output:
```
kmwang14@corn21:~/CME211/Cplusplus/lecture-17$ ./a.out
a[0][0] = 0
a[0][1] = 1
a[0][2] = 2
a[1][0] = 3
a[1][1] = 4
a[1][2] = 5
```
### Reading and Writing files using `fstream`

Can read and write using fstream

```cpp
#include <iostream>
#include <fstream>

int main() {
  std::fstream f;

  // specify output mode with second argument
  f.open("hello.txt", std::ios::out); // need to pass in what you want to do (read? or write?)
  if (f.is_open()) {
    f << "Hello" << std::endl;
    f.close();
  }
  else {
    std::cout << "Failed to open file" << std::endl;
  }

  return 0;
}
```

## Reading

* **C++ Primer, Fifth Edition** by Lippman et al.

* Chapter 1: Conditional Statements: Sections 5.3 - 5.5
* Chapter 8: The IO Library: Section 8.2

# CME 211: Lecture 17

Friday, October 30, 2015

### Command line arguments
- Argc is the number of input arguments
- Float returns 32 bit floating point
- Double returns a 64 bit floating point

```cpp
#include <iostream>

int main(int argc, char *argv[]) {
  // Display the command line arguments
  for (int n = 0; n < argc; n++) {
    std::cout << n << " " << argv[n] << std::endl;
  }
  return 0;
}
```

```
$ ./argv1 hello.txt 3.14 42
0 ./argv1 // first one is the program
1 hello.txt
2 3.14
3 42
$
```

### Command line arguments

```cpp
#include <iostream>
#include <string>

int main(int argc, char *argv[]) {
  if (argc < 4) {
    std::cout << "Usage:" << std::endl;
    std::cout << " " << argv[0] << " <filename> <param1> <param2>" << std::endl;
    return 0;
  }

  std::string filename = argv[1];
  // conversion functions (from char array to numbers) comes from <string>
  double param1 = std::stod(argv[2]); //stof gives a double number
  int param2 = std::stoi(argv[3]);   //stoi gives an int

  std::cout << "filename = " << filename << std::endl;
  std::cout << "param1 = " << param1 << std::endl;
  std::cout << "param2 = " << param2 << std::endl;

  return 0;
}
```

Output:

```
$ g++ -std=c++11 -Wall -Wconversion -Wextra argv2.cpp -o argv2
$ ./argv2 hello.txt 3.14 42
filename = hello.txt
param1 = 3.14
param2 = 42
$
```

### Formatting
Note: can also do #include <cstdio> for C style printing
```cpp
#include <iostream>

int main() {
  double a = 2.;
  std::cout << "a = " << a << std::endl;
  return 0;
}
```

```
$ ./formatting1
a = 2
$
```

### Showing decimal point

```cpp
#include <iostream>

int main() {
  double a = 2.;
  //this changes a flag (setf means set floating point property), then () is the options
  std::cout.setf(std::ios::showpoint);
  std::cout << "a = " << a << std::endl;
  return 0;
}
```
// Prints out a bunch of zeros
```
$ ./formatting2
a = 2.00000
$
```

### Showing decimal point

```cpp
#include <iostream>

int main() {
  double a = 2., b = 3.14;
  int c = 4;

  std::cout.setf(std::ios::showpoint);

  std::cout << "a = " << a << std::endl;
  std::cout << "b = " << b << std::endl;
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

```
$ ./formatting3
a = 2.00000
b = 3.14000
c = 4
$
```

### Controlling decimal places
- note: the int is still just 4

```c++
#include <iostream>

int main() {
  double a = 2., b = 3.14;
  int c = 4;

  //Always show 3 decimal places
  //fixed means "fixed width"
  std::cout.setf(std::ios::fixed, std::ios::floatfield);
  std::cout.setf(std::ios::showpoint); //always show the point
  std::cout.precision(3);

  std::cout << "a = " << a << std::endl;
  std::cout << "b = " << b << std::endl;
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

```
$ ./formatting4
a = 2.000
b = 3.140
c = 4
$
```

### Scientific notation
- note: the int is still just 4

```cpp
int main() {
  double a = 2., b = 3.14;
  int c = 4;

  std::cout.setf(std::ios::scientific, std::ios::floatfield);
  std::cout.precision(3);

  std::cout << "a = " << a << std::endl;
  std::cout << "b = " << b << std::endl;
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

```
$ ./formatting5
a = 2.000e+00
b = 3.140e+00
c = 4
$
```

### Field width

```cpp
#include <iostream>

int main() {
  double a = 2., b = 3.14;
  int c = 4;
  std::cout.setf(std::ios::scientific, std::ios::floatfield);
  std::cout.precision(3);

  std::cout << "a = " << a << std::endl;
  std::cout.width(15);
  std::cout << "b = " << b << std::endl;
  std::cout.width(30);
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

```
$ ./formatting6
a = 2.000e+00
           b = 3.140e+00
                          c = 4
$
```

### Fill character

```cpp
#include <iomanip>
#include <iostream>

int main() {

  std::cout.fill('0'); // use this to line stuff up

  for(int n = 0; n < 10; n++) {
    std::cout << std::setw(2) << n << std::endl;
  }

  return 0;
}
```
Width is 2 and fill character is 0
```
$ ./formatting7
00
01
02
...
```
### Left and Right Justify

```cpp
#include <iomanip>
#include <iostream>

int main() {

  std::cout.fill('0');

  //left justify
  std::cout << "Left Justify: " << std::endl;
  for(int n = 0; n < 3; n++) {
    std::cout << std::left;
    std::cout << std::setw(12) << n << std::endl;
  }

  //right justify
  std::cout << "Right Justify: " << std::endl;
  for(int n = 0; n < 3; n++) {
    std::cout << std::right;
    std::cout << std::setw(12) << n << std::endl;
  }


  return 0;
}
```
Output:
```
kmwang14@corn20:~/CME211/Cplusplus/lecture-17$ ./a.out
Left Justify:
000000000000
100000000000
200000000000
Right Justify:
000000000000
000000000001
000000000002
```
### Output for Files
- cout and files work the same

```cpp
#include <iostream>
#include <fstream>

int main() {
  double a = 2., b = 3.14;
  int c = 4;

  std::ofstream f("formatting.txt");
  f.setf(std::ios::showpoint);

  f << "a = " << a << std::endl;
  f << "b = " << b << std::endl;
  f << "c = " << c << std::endl;

  f.close();

  return 0;
}
```

```
$ ./formatting8
$ cat formatting.txt
a = 2.00000
b = 3.14000
c = 4
$
```

### Loading a table

Remember the Movielens data?

```
$ cat u.data
196	242	3	881250949
186	302	3	891717742
22	377	1	878887116
244	51	2	880606923
166	346	1	886397596
298	474	4	884182806
115	265	2	881171488
253	465	5	891628467
305	451	3	886324817
6	86	3	883603013
```

### Same data on each line
Note: Macs use clang++ (different compiler compared to corn)
- from inputstream f, read into uid, mid, rating, and time
- reads in a single line at a time and delimit by whitespace, then store info in those variables
- >> stream operator converts what it reads from file into an integer
- if we don't include >> time, it messes up the column since now it doesn't read in the time stamp
```cpp
#include <fstream>
#include <iostream>

int main() {

  std::ifstream f;
  f.open("u.data");
  if (f.is_open()) {
    int uid, mid, rating, time;

    while (f >> uid >> mid >> rating >> time) { //returns false at end of file
      std::cout << "user = " << uid;
      std::cout << ", movie = " << mid;
      std::cout << ", rating = " << rating << std::endl;
    }
    f.close();
  }
  else {
    std::cerr << "ERROR: Failed to open file" << std::endl;
  }
  return 0;
}
```

```
$ ./file1
user = 196, movie = 242, rating = 3
user = 186, movie = 302, rating = 3
user = 22, movie = 377, rating = 1
user = 244, movie = 51, rating = 2
user = 166, movie = 346, rating = 1
user = 298, movie = 474, rating = 4
user = 115, movie = 265, rating = 2
user = 253, movie = 465, rating = 5
user = 305, movie = 451, rating = 3
user = 6, movie = 86, rating = 3
```

### Different data types

See `src/dist.female.first`:

```
MARY           2.629  2.629      1
PATRICIA       1.073  3.702      2
LINDA          1.035  4.736      3
BARBARA        0.980  5.716      4
ELIZABETH      0.937  6.653      5
JENNIFER       0.932  7.586      6
MARIA          0.828  8.414      7
SUSAN          0.794  9.209      8
MARGARET       0.768  9.976      9
DOROTHY        0.727 10.703     10
LISA           0.704 11.407     11
NANCY          0.669 12.075     12
KAREN          0.667 12.742     13
BETTY          0.666 13.408     14
```

### Be careful with data types

```cpp
std::ifstream f;

f.open("dist.female.first");
if (f.is_open()) {
  std::string name;
  double perc1, perc2;
  int rank;
  while (f >> name >> perc1 >> perc2 >> rank) {
    std::cout << name << ", " << perc1 << std::endl;
  }
  f.close();
}
else {
  std::cerr << "ERROR: Failed to open file" << std::endl;
}
```

### Step by step extraction

What if lines have a varying amount of data to load?

```
$ cat geometry1.txt
workspace 0 0 10 10
circle 3 7 1
triangle 4 6 8 6 5 7
rectangle 1 1 8 2
$ cat geometry2.txt
workspace 0 0 10 10
circle 3 7 1
line 0 0 3 2
rectangle 1 1 8 2
$
```

### Step by step extraction

```cpp
f.open(filename);
if (f.is_open()) {
  std::string shape;
  while (f >> shape) {
    int nval;
    // Determine the shape and how many values need to be read
    if (shape == "workspace" or shape == "rectangle")
      nval = 4;
    else if (shape == "circle")
      nval = 3;
    else if (shape == "triangle")
      nval = 6;
    else {
      std::cerr << "ERROR: Unknown shape '" << shape;
      std::cerr << "'" << std::endl;
      return 1;
    }

    // Read appropriate number of values
   float val[6];
   for (int n = 0; n < nval; n++) {
   f >> val[n];
  }
```

### Read line by line

```cpp
f.open(filename);
if (f.is_open()) {
  std::string line;
  while (getline(f, line)) { // reads a single line from file into a string, handy to process line by line
    std::cout << line << std::endl;
  }
  f.close();
}
else {
  std::cerr << "ERROR: Failed to open file" << std::endl;
}
```

### Read line by line

```
$ ./file4 geometry1.txt
workspace 0 0 10 10
circle 3 7 1
triangle 4 6 8 6 5 7
rectangle 1 1 8 2
$ ./file4 geometry2.txt
workspace 0 0 10 10
circle 3 7 1
line 0 0 3 2
rectangle 1 1 8 2
$
```

### String stream READ IN BOOK
Remains in memory- Purpose is to convert back into string later
This is a more efficient way to do string concatenation and behind the scenes, object handles mem by itself
```cpp
f.open(filename);
if (f.is_open()) {
  // Read the file one line at a time
  std::string line;
  while (getline(f, line)) {
    // Use a string stream to extract text for the shape
    std::stringstream ss;
    ss << line;
    std::string shape;
    ss >> shape;

    // Determine how many values need to be read
    int nval;
    if (shape == "workspace" or shape == "rectangle")
    nval = 4;
    ...
else {
  std::cerr << "ERROR: Unknown shape '" << shape;
  std::cerr << "'" << std::endl;
  return 1;
}
// Read appropriate number of values
float val[6];
for (int n = 0; n < nval; n++)
  ss >> val[n]
```

```
$ ./extraction1
Usage:
./extraction1 <name data> [nnames]

Read at most nnames (optional)
$
```

### Convert argument to number
- use 'std::stoi' for string to int
- use 'std::stof' for string to Float
- use 'std::stod' for string to double

```cpp
#include <limits>

int main(int argc, char *argv[]) {
  if (argc < 2) {
    std::cout << "Usage:" << std::endl;
    std::cout << " " << argv[0] << " <name data> [nnames]" << std::endl << std::endl;
    std::cout << " Read at most nnames (optional)" << std::endl;
    return 0;
  }

  // Setup string for the filename to be read
  std::string filename = argv[1];

  // Determine maximum number of names to read
  int nnames = std::numeric_limits<int>::max(); //do #include <limits> to retrieve limit of integer type
  if (argc == 3) {
    nnames = std::stoi(argv[2]); //again, we see stoi. also, this is NOT a silent error (see Testing extraction)
  }

  std::ifstream f;
  f.open(filename);
```

### Convert argument to number

```
$ ./extraction1 dist.female.first
Read 10 names.
$ ./extraction1 dist.female.first 7
Read 7 names.
$ ./extraction1 dist.female.first 3
Read 3 names.
```

### Testing extraction

```cpp
#include <iostream>
#include <sstream>

int main(int argc, char *argv[]) {
  // Setup a string stream to access the command line argument
  std::string arg = argv[1];
  std::stringstream ss;
  ss << arg;

  // Attempt to extract an integer from the string stream
  int n = 0;
  ss >> n; //putting a string into an int, and when C++ realizes it can't convert, it leaves n=0 and doens't modify it. This doesn't throw an error! or a silent error
  std::cout << "n = " << n << std::endl;

  return 0;
}
```

### Testing extraction

```
$ ./extraction2 42
n = 42
$ ./extraction2 -17
n = -17
$ ./extraction2 hello
n = 0
```

### Extraction failures

```cpp
#include <iostream>
#include <sstream>

int main(int argc, char *argv[]) {
  // Setup a string stream to access the command line argument
  std::string arg = argv[1];
  std::stringstream ss;
  ss << arg;

  // Attempt to extract an integer from the string stream
  int n = 0;
  if (ss >> n) // if cannot convert, it returns a false
    std::cout << "n = " << n << std::endl;
  else
    std::cerr << "ERROR: string stream extraction failed" << std::endl;

  return 0;
}
```

### Extraction failures

```
$ ./extraction3
n = 42
$ ./extraction3
n = -17
$ ./extraction3
ERROR: string stream extraction failed
$ ./extraction3
n = 3
$
```

## Reading

* **C++ Primer, Fifth Edition** by Lippman et al.

* Chapter 8: The IO Library

* Chapter 17: Specialized Library Facilities: Section 17.5.1

# CME 211: Lecture 18

Wednesday, November 4, 2015

Topics:

* Functions
* Preprocessor & `#include` statements

## Functions

* Functions allow us to decompose a program into smaller components

* It is easier to implement, test, and debug portions of a program in isolation

### C/C++ function

Example:

```c++
int sum(int a, int b) { //need to specify return type and parameter type
  int c = a + b;
  return c;
}
```

Components:

```c++
return_type function_name(argument_type1 argument_var1, ...) {
   // function body
   return return_var; // return_var must have return_type
}
```

### `sum` function in use

`src/sum1.cpp`

```c++
#include <iostream>

int sum(int a, int b) {
  int c = a + b;
  return c;
}

int main() {
  int a = 2, b = 3;

  int c = sum(a,b);
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion sum1.cpp -o sum1
$ ./sum1
c = 5
$
```

### Order matters

`src/sum2.cpp`:

The order in which the function appears matters. Sum function should show up before the main function.

```c++
#include <iostream>

int main() {
  int a = 2, b = 3;

  // the compiler does not yet know about sum()
  int c = sum(a,b);
  std::cout << "c = " << c << std::endl;

  return 0;
}

int sum(int a, int b) {
  int c = a + b;
  return c;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion sum2.cpp -o sum2
sum2.cpp: In function 'int main()':
sum2.cpp:7:18: error: 'sum' was not declared in this scope
  int c = sum(a,b);
                 ^
$
```

### Function declarations and definitions

* A function *definition* is the code that implements the function

* It is legal to call a function if it has been defined or *declared* previously (in the same C++ source file)

* See example below: A function *declaration* specifies the function name, input argument type(s),
  and output type.  The function *declaration* need not specify the
  implementation (code) for the function.

`src/sum3.cpp`:

```c++
#include <iostream>

// Forward declaration or prototype
int sum(int a, int b); // tells compiler that there is an implementation for this function and it exists somewhere (can put in separate .hpp file)

int main() {
  int a = 2, b = 3;

  int c = sum(a,b);
  std::cout << "c = " << c << std::endl;

  return 0;
}

// Function definition
int sum(int a, int b) {
  int c = a + b;
  return c;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion sum3.cpp -o sum3
$ ./sum3
c = 5
$
```

### Data types

`src/datatypes1.cpp`

```c++
#include <iostream>

int sum(int a, int b) {
  int c;
  c = a + b;
  return c;
}

int main() {
  double a = 2.7, b = 3.8;

  int c = sum(a,b);
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion datatypes1.cpp -o datatypes1
datatypes1.cpp: In function 'int main()':
datatypes1.cpp:14:18: warning: conversion to 'int' from 'double' may alter its value [-Wconversion]
  int c = sum(a,b);
              ^
datatypes1.cpp:14:18: warning: conversion to 'int' from 'double' may alter its value [-Wconversion]
$ ./datatypes1
c = 5
$
```

### Implicit casting

`src/datatypes2.cpp`:

```c++
#include <iostream>

int sum(int a, int b) {
  double c = a + b;
  return c; // we are not returning the correct type (since this rounds c up or down to an int)

}

int main() {
  double a = 2.7, b = 3.8;

  int c = sum(a,b);
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion datatypes2.cpp -o datatypes2
datatypes2.cpp: In function 'int sum(int, int)':
datatypes2.cpp:6:10: warning: conversion to 'int' from 'double' may alter its value [-Wconversion]
  return c;
         ^
datatypes2.cpp: In function 'int main()':
datatypes2.cpp:13:18: warning: conversion to 'int' from 'double' may alter its value [-Wconversion]
  int c = sum(a,b);
              ^
datatypes2.cpp:13:18: warning: conversion to 'int' from 'double' may alter its value [-Wconversion]
$ ./datatypes2
c = 5
$
```

### Explicit casting

`src/datatypes3.cpp`

```c++
#include <iostream>

int sum(int a, int b) {
  double c = a + b;
  return (int)c;
}

int main() {
  double a = 2.7, b = 3.8;

  int c = sum((int)a,(int)b); // can also do int(a), but (int)a used to be compatible with C
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

Output:
This returns c = 5

```
$ g++ -Wall -Wextra -Wconversion datatypes3.cpp -o datatypes3
$
```


### `void`

* Use the `void` keyword to indicate absence of data

* `src/void1.cpp`

```c++
#include <iostream>

void printHeader(void) { //the input argument doesn't need to have anything (it's optional to put void or not)
  std::cout << "-------------------------" << std::endl;
  std::cout << "      MySolver v1.0      " << std::endl;
  std::cout << "-------------------------" << std::endl;
}

int main() {
  printHeader();
  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion void1.cpp -o void1
$ ./void1
-------------------------
      MySolver v1.0
-------------------------
$
```

### `void` and `return`

`src/void2.cpp`:

```c++
#include <iostream>

//returning 0 gives compiler error since the return is void
void printHeader(void) {
  std::cout << "-------------------------" << std::endl;
  std::cout << "      MySolver v1.0      " << std::endl;
  std::cout << "-------------------------" << std::endl;
  return 0;
}


int main() {
  printHeader();
  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion void2.cpp -o void2
void2.cpp: In function 'void printHeader()':
void2.cpp:8:10: error: return-statement with a value, in function returning 'void' [-fpermissive]
  return 0;
         ^
$
```

### `void` and `return`

`src/void3.cpp`:

```c++
#include <iostream>

// use return keyword to get back to main
void printHeader(void) {
  std::cout << "-------------------------" << std::endl;
  std::cout << "      MySolver v1.0      " << std::endl;
  std::cout << "-------------------------" << std::endl;
  return;
}

int main() {
  printHeader();
  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion void3.cpp -o void3
$
```
and it gives
```
-------------------------
      MySolver v1.0      
-------------------------
```

### Ignoring return value

`src/ignore.cpp`:

```c++
#include <iostream>

int sum(int a, int b) {
  int c = a + b;
  return c;
}

int main() {
  int a = 2, b = 3;

  sum(a,b); // legal to ignore return value if you want

  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion ignore.cpp -o ignore
$ ./ignore
$
```

### Function scope

`src/scope1.cpp`:

```c++
#include <iostream>

int sum(void) {
  // a and b are not in the function scope
  int c = a + b;
  return c;
}

int main() {
  int a = 2, b = 3;

  int c = sum();
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

Output:

In this case, a,b exist in scope of main but not in sum, so it gives a compile time error

```
$ g++ -Wall -Wextra -Wconversion scope1.cpp -o scope1
scope1.cpp: In function 'int sum()':
scope1.cpp:5:11: error: 'a' was not declared in this scope
  int c = a + b;
          ^
scope1.cpp:5:15: error: 'b' was not declared in this scope
  int c = a + b;
              ^
...
```

### Global scope

`src/scope2.cpp`:

```c++
#include <iostream>

// an be accessed from anywhere in the file (bad, bad, bad)
int a;

void increment(void) {
  a++;
}

int main() {
  a = 2;

  std::cout << "a = " << a << std::endl;
  increment();
  std::cout << "a = " << a << std::endl;

  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion scope2.cpp -o scope2
$ ./scope2
a = 2
a = 3
$
```

### Passing arguments
- a is incremented in the function, which only has a local scope
- thus, when a is printed after the function increment(), its value remains the same as it was initialized

`src/passing1.cpp`:

```c++
#include <iostream>

void increment(int a) {
  a++;
  std::cout << "a = " << a << std::endl;
}

int main() {
  int a = 2;

  increment(a);
  std::cout << "a = " << a << std::endl;

  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion passing1.cpp -o passing1
$ ./passing1
a = 3
a = 2
$
```

### Passing arguments
- when passing a static array, the pointer is passed so values are modified

`src/passing2.cpp`:

```c++
#include <iostream>

void increment(int a[2]) {
  a[0]++;
  a[1]++;
  std::cout << "a[0] = " << a[0]<<", " << "a[1] = " << a[1] << std::endl;
}

int main() {
  int a[2] = {2, 3};

  std::cout << "a[0] = " << a[0]<<", " << "a[1] = " << a[1] << std::endl;
  increment(a);
  std::cout << "a[0] = " << a[0]<<", " << "a[1] = " << a[1] << std::endl;
  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion passing2.cpp -o passing2
$ ./passing2
a[0] = 2, a[1] = 3 //before function
a[0] = 3, a[1] = 4 //in function
a[0] = 3, a[1] = 4 //after function
$
```

### Pass by value

* C/C++ default to pass by value, which means that when calling a function the arguments are **copied**

* However, you need to be careful and recognize what is being copied

* In the case of a number like `int a`, what is being copied is the value of the
  number

* For a static array like `int a[2]`, what is being passed and copied is the
**location in memory** where the array data is stored

* Will discuss pass by reference when we get to data structures


### Towards modularity
Takes source file and compiles them independently
Compiler operates in stages:
1. preprocess
2. compile
3. link


`src/main4.cpp`:

```c++
#include <iostream>

int sum(int a, int b); //declare function in main

int main() {
  int a = 2, b = 3;

  int c = sum(a,b);
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

`src/sum4.cpp`:

```c++
int sum(int a, int b) {
  int c = a + b;
  return c;
}
```

Output:
g++ -Wall -Wextra -Wconversion main4.cpp sum4.cpp -o sum4
- this command links  main4.cpp sum4.cpp  together
- if don't include one of the program, this gives a "linker" or "link" error
- don't need to #include sum4.cpp in main since linking it together will make C++ search for the file (also we declared function in main)
```
$ g++ -Wall -Wextra -Wconversion main4.cpp sum4.cpp -o sum4
$ ./sum4
c = 5
$
```

### Maintaining consistency
- our function declaration doesn't match the function we wrote, so we get a link time error.
`src/main5.cpp`:

```c++
#include <iostream>

int sum(int a, int b);

int main() {
  int a = 2, b = 3;

  int c = sum(a,b);
  std::cout << "c = " << c << std::endl;

  return 0;
}
```

`src/sum5.cpp`:

```c++
double sum(double a, double b) {
  double c = a + b;
  return c;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion main5.cpp sum5.cpp -o sum5
/tmp/ccCKlsvX.o: In function main':
main5.cpp:(.text+0x21): undefined reference to sum(int, int)'
collect2: error: ld returned 1 exit status
$
```

- **Can overload a function based on input arguments, not output**

## The preprocessor and `#include`

* We have used functionality from the C++ standard library for output to the
screen using `cout`, performing I/O with files, using the string object, etc.

* A library is a collection of functions, data types, constants, class
definitions, etc.

* Somewhat analogous to a Python module

* At a minimum, accessing the functionality of a library requires `#include`
statements

### `#include`

* So what actually happens when you put something like `#include <iostream>` in
your file?

* `<iostream>` is a way of referring to a file called iostream that is part of
the compiler installation and on the corn machines is found at
`/usr/include/c++/4.8/iostream`

* These types of files are called include or header files and contains forward
declarations (prototypes) of functions, class definitions, constants, etc.

### Preprocessor

* Before files are processed by the compiler, they are run through the C
preprocessor, `cpp`

* What does the preprocessor do?

* For one thing it processes those `#include` statements
    - "#include" copy pastes into code

### Hacking the preprocessor

```
$ cpp -P goodbye.txt
Hello!

Goodbye!


$ cat hello.txt
Hello!
$ cat goodbye.txt
#include "hello.txt"
Goodbye!

$ cpp -P goodbye.txt
Hello!

Goodbye!
```


### Compilation process

![fig](lecture-18/fig/compilation.png)

### Standard decomposition

* Function (and type) *declarations* go in header (`.hpp`) files

* Function *definitions* go in source (`.cpp`) files

* Source files that want to use the functions must `#include` the header

`src/main6.cpp`:

```c++
```

`src/sum6.hpp`

```c++
double sum(double a, double b);
```

`src/sum6.cpp`:

```c++
#include "sum6.hpp"

double sum(double a, double b) {
  double c = a + b;
  return c;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion main6.cpp sum6.cpp -o sum6
$ ./sum6
c = 5
$
```

### `#include` syntax

* The `.hpp` file extension denotes a C++ header file

* for `#include <iostream>`, (or anything with `<blah>`), preprocessor searches for an include file in a system dependent or default directory

  -  Usually these files are somewhere in `/usr/include` with the GNU compilers on Linux

* `"header.hpp"` means that the preprocessor should first search in the user directory,
followed by a search in a system dependent or default directory if necessary


### `#define`

- Will replace any instance of ni or nj with 16
- Use this for constants

`src/define1.cpp`:

```c++
// define ni and nj to be 16

#define ni 16
#define nj 16

int main() {
  int a[ni][nj];

  for(int i = 0; i < ni; i++) {
    for(int j = 0; j < nj; j++) {
      a[i][j] = 1;
    }
  }

  return 0;
}
```

Pass the code through the preprocessor:

```
$ cpp -P define1.cpp
// define ni and nj to be 16

int main() {
  int a[16][16];

  for(int i = 0; i < 16; i++) {
    for(int j = 0; j < 16; j++) {
      a[i][j] = 1;
    }
  }

  return 0;
}
$
```

### Macros

* Real power of `#define` is in setting up macros

* Similar to functions but handled by the preprocessor

### `#define` macro

`src/define2.cpp`

```c++
#include <iostream>

#define sqr(n) (n)*(n) // take instances of sqr(n) and replace it with (n)*(n), so do this for generic types

int main() {
  int a = 2;

  int b = sqr(a);
  std::cout << "b = " << b << std::endl;

  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion define2.cpp -o define2
$ ./define2
b = 4
$
```

### Be careful to include  `()`
- with no `()`, we got a+ `3*a +3 = 11` instead of `(a+3)*(a+3) = 25`

`src/define3.cpp`:

```c++
#include <iostream>

#define sqr(n) n*n // need to bracket variables!! otherwise get weird outputs  

int main() {
  int a = 2;

  int b = sqr(a+3);
  std::cout << "b = " << b << std::endl;

  return 0;
}
```

Output:
```
$ g++ -Wall -Wextra -Wconversion define3.cpp -o define3
$ ./define3
b = 11
$
```

### Predefined macros

`src/define4.cpp`:


```c++
#include <iostream>

int main() {
  std::cout << "This line is in file " << __FILE__
            << ", line " << __LINE__ << std::endl;
  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion define4.cpp -o define4
$ ./define4
This line is in file define4.cpp, line 5
$
```

### Conditional compilation
- specify debug on command line to run `-D` is predefined variable

`src/conditional.cpp`:

```c++
#include <iostream>

#define na 4

int main() {
  int a[na];

  a[0] = 2;
  for (int n = 1; n < na; n++) a[n] = a[n-1] + 1;

#ifdef DEBUG // if DEBUG is defined in the preprocessor, then include this code. This is a debugging flag
  // Only kept by preprocessor if DEBUG defined
  for (int n = 0; n < na; n++) {
    std::cout << "a[" << n << "] = " << a[n] << std::endl;
  }
#endif

  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion conditional.cpp -o conditional
$ ./conditional
$ g++ -Wall -Wextra -Wconversion conditional.cpp -o conditional -DDEBUG
$ ./conditional
a[0] = 2
a[1] = 3
a[2] = 4
a[3] = 5
$
```

### Reading

* **C++ Primer, Fifth Edition** by Lippman et al.

* Chapter 6: Functions: Sections 6.1 - 6.3

# CME 211: Lecture 19

Friday, November 6, 2015

Topics:

* C++ containers
* `vector`
* `tuple`

## C++ containers

* Static arrays are very limiting

* You could build your own data structures like lists, dictionaries, etc.

* But the C++ standard library includes many containers that are similar to what
you have already seen in Python

* Some of these include: `vector`, `map`, `set`, `tuple`, etc.

## Vector

* A vector in C++ is analogous to a list in Python

* Vectors are objects, so they have methods associated with them

* Just like the Python list, a vector can change in size to accommodate the
addition or removal of items

* Unlike Python lists, the vector is restricted to containing homogeneous data

### Vectors: size.(), empty(), push_back()
- Note: can use emplace_back() if we #include <tuple>

`src/vector1.cpp`:

```c++
#include <iostream>
#include <vector>

int main()
{
  std::vector<int> v;

  std::cout << "v.size() = " << v.size() << std::endl;

  if (v.empty())
    std::cout << "v is empty" << std::endl;
  else
    std::cout << "v is not empty" << std::endl;

  v.push_back(42); //similar to "append()" to a list in python

  std::cout << "v.size() = " << v.size() << std::endl;

  if (v.empty())
    std::cout << "v is empty" << std::endl;
  else
    std::cout << "v is not empty" << std::endl;

  return 0;
}
```

Output:

```
$ g++ -Wall -Wextra -Wconversion vector1.cpp -o vector1
$ ./vector1
v.size() = 0
v is empty
v.size() = 1
v is not empty
$
```

### Printing a vector

We must write our own loop to print a vector.  We use square brackets `[]` to
access an item of a `vector`.

`src/vector3.cpp`:

```c++
#include <iostream>
#include <vector>

int main()
{
  std::vector<int> v;
  v.push_back(42);
  v.push_back(-7);
  v.push_back(19);

  for(unsigned int n = 0; n < v.size(); n++)
    std::cout << "v[" << n << "] = " << v[n] << std::endl;

  return 0;
}
```

Output:

```
$ g++ -std=c++11 -Wall -Wextra -Wconversion    vector3.cpp   -o vector3
$ ./vector3
v[0] = 42
v[1] = -7
v[2] = 19
```

### `operator[]`

On C++ containers, like `vector`, the square brakets `[]` are called
`operator[]`.  This is a special method for C++ objects and may be overloaded.
For now, we just need to use them for `vector`s.

Valid `vector` indices for a vector named `v` are in the range
`[0,v.size())`.  Attempting to access element outside of those bounds leads to undefined behavior.  

### Vector Access Out of Bounds
- Leads to undefined behavior, but no error
- Look at file `src/vector4a.cpp` for example of this
- Can check for access out of bounds using `-fsanitize=address` compiler flag
- Sometimes address sanitizer won't give a fault, but we get junk output for undefined behavior (like accessing v[3] when vector is only {1 2 3})

- Explanation of above:
* When a `vector` is declared in C++, some amount of memory is allocated for the
  storage of the element.  Often, more storage is allocated than initially
  needed by the vector to allow for efficient addition of new items at the end
  of the vector.

* Thus, trying to access `v[3]` in this case does not access memory out of
  bounds from the context of the lower level memory allocation, but is still
  undefined behavior.  There is not guarantee that there will be extra space.

* `operator[]` for `vector` takes in an **unsigned integer** as its argument.  Therefore in `v[-1]` the `-1` is converted to a very large positive integer, which
  turns out to be out of range of the allocated memory for the vector.  This
  leads to the address sanitizer churning out error messages.

### `at()` for bounds checking

The `at()` method for a vector performs bounds checking.  As a result `at()` is
slower than `operator[]`.

`src/vector5.cpp`:

```c++
#include <iostream>
#include <vector>

int main()
{
  std::vector<int> v;
  v.push_back(42);
  v.push_back(-7);
  v.push_back(19);

  std::cout << "v.at(1) = " << v.at(1) << std::endl;
  std::cout << "v.at(3) = " << v.at(3) << std::endl;

  return 0;
}
```
error is not connected to sanitizer- this is from .at() methods
Watch out, at() adds extra computation- can help in debugging, but would rather use debug move
Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion -g -fsanitize=address    vector5.cpp   -o vector5
$ ./vector5
v.at(1) = -7
libc++abi.dylib: terminating with uncaught exception of type std::out_of_range: vector
```

(I am at home writing these notes on my Mac.  You will see `clang++` as the
compiler.  For the context of this class consider this to be equivalent to `g++`.)

### Modifying an element

`src/vector6.cpp`:

```c++
#include <iostream>
#include <vector>

int main()
{
  std::vector<int> v;
  v.push_back(42);
  v.push_back(-7);
  v.push_back(19);

  v[1] = 73;

  for(unsigned int n = 0; n < v.size(); n++)
    std::cout << "v[" << n << "] = " << v[n] << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion -g -fsanitize=address    vector6.cpp   -o vector6
$ ./vector6
v[0] = 42
v[1] = 73
v[2] = 19
```
### Iterators (see below for examples): insert, erase, sort, accumulate, begin(), end()
### Insert into vector: need to use an **Iterator**

We have to us an **iterator** for this.
- C++ `vector` does not allow insertion at an integer index.
- think of iterator like a reference/pointer to a particular location to a vector
`src/vector8.cpp`:

```c++
#include <iostream>
#include <vector>

int main()
{
  std::vector<int> v;
  v.push_back(42);
  v.push_back(-7);
  v.push_back(19);

  //  Declare an iterator
  std::vector<int>::iterator iter;

  // Set iterator to start of vector
  iter = v.begin(); //points to start of vector

  // Advance iterator by two positions
  iter += 2;

  // Use iterator to insert a new value into the vector
  v.insert(iter, 73);

  for(unsigned int n = 0; n < v.size(); n++)
    std::cout << "v[" << n << "] = " << v[n] << std::endl;

  return 0;
}
```
Inserted 73, and it pushed 19 back
Data containers don't have a notion of being able to index into the container, so we need iterator
(ex, C++ map doesn't have a sequence, so iterator moves thru items in a container but not be associated with an integer index)
Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion -g -fsanitize=address    vector8.cpp   -o vector8
$ ./vector8
v[0] = 42
v[1] = -7
v[2] = 73
v[3] = 19
```

### Erase: Need to use **Iterator**

The `erase()` method also uses an iterator.
Below example, we want to remove the fourth element

`src/vector9.cpp`:

```c++
#include <iostream>
#include <vector>

int main()
{
  std::vector<int> v;
  v.push_back(42);
  v.push_back(-7);
  v.push_back(19);
  v.push_back(73);
  v.push_back(0);

  // remove fourth element
  v.erase(v.begin()+3); //advances three elements from beginning

  for(unsigned int n = 0; n < v.size(); n++)
    std::cout << "v[" << n << "] = " << v[n] << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion -g -fsanitize=address    vector9.cpp   -o vector9
$ ./vector9
v[0] = 42
v[1] = -7
v[2] = 19
v[3] = 0
```

### Sort, begin(), end(): Need to use **Iterator**
- Need to `#include <algorithm>` to use std::sort
- v.begin() and v.end() returns an iterator pointing to the first/last element in vector

`src/sort.cpp`:

```c++
#include <algorithm>
#include <iostream>
#include <vector>

int main()
{
  std::vector<int> v;
  v.push_back(42);
  v.push_back(-7);
  v.push_back(19);
  v.push_back(73);
  v.push_back(0);

  std::sort(v.begin(), v.end());

  for(unsigned int n = 0; n < v.size(); n++)
    std::cout << "v[" << n << "] = " << v[n] << std::endl;

  return 0;
}

```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion -g -fsanitize=address    sort.cpp   -o sort
$ ./sort
v[0] = -7
v[1] = 0
v[2] = 19
v[3] = 42
v[4] = 73
```

### Accumulate: Need to use **Iterator**
Need to include numeric algorithm. Need to pass it iterators of the beginning and end. Check reference for waht 0 is doing.
This is equivalent to python sum()

`src/accumulate.cpp`:

```c++
#include <iostream>
#include <numeric>
#include <vector>

int main()
{
  std::vector<int> v;
  v.push_back(42);
  v.push_back(-7);
  v.push_back(19);
  v.push_back(73);
  v.push_back(0);

  int sum = std::accumulate(v.begin(), v.end(), 0);
  std::cout << "sum = " << sum << std::endl;

  return 0;
}
```

Output:

```
$ ./accumulate
sum = 127
$
```

### Copies
- writing `std::vector<int> v2 = v1;` for two vectors gives a COPY
- see `src/vector10.cpp` for example

### Function that returns a vector

`src/vector11.cpp`:

```c++
#include <iostream>
#include <fstream>
#include <vector>

std::vector<int> ReadNumbers(std::string filename) {
  std::vector<int> v;
  std::ifstream f(filename.c_str());
  if (f.is_open()) {
    int val;
    while (f >> val) v.push_back(val);
    f.close();
  }
  return v;
}

int main() {
  std::vector<int> v = ReadNumbers("numbers.txt");

  for(unsigned int n = 0; n < v.size(); n++)
    std::cout << "v[" << n << "] = " << v[n] << std::endl;

  return 0;
}
```

Output:

```
$ cat numbers.txt
42
17
-5
73
$ ./vector11
v[0] = 42
v[1] = 17
v[2] = -5
v[3] = 73
$
```

### Passing vector into a function makes a COPY

`src/vector12.cpp`:

```c++
#include <iostream>
#include <vector>

//this is pass by caller (make a copy)
void increment(std::vector<int> v) {
  for (unsigned int n = 0; n < v.size(); n++) {
    v[n]++;
    std::cout << "v[" << n << "] = " << v[n] << std::endl;
  }
}

int main() {
  std::vector<int> v;
  v.push_back(42);
  v.push_back(-7);
  v.push_back(19);

  increment(v);

  for (unsigned int n = 0; n < v.size(); n++) {
    std::cout << "v[" << n << "] = " << v[n] << std::endl;
  }
  return 0;
}
```

Output:

```
$ ./vector12
v[0] = 43
v[1] = -6
v[2] = 20
v[0] = 42
v[1] = -7
v[2] = 19
$
```

### Passing values by REFERENCE
This is faster as the previous example
`src/passing.cpp`:

```c++
#include <iostream>

void increment(int &a) //use & to pass by reference; variable is reference to an int
{
  a++;
  std::cout << "a = " << a << std::endl;
}

int main()
{
  int a = 2;

  increment(a);
  std::cout << "a = " << a << std::endl;

  return 0;
}
```

Output:

```
$ ./passing
a = 3
a = 3
$
```

### Passing vector by REFERENCE

`src/vector13.cpp`:

```c++
#include <iostream>
#include <vector>

void increment(std::vector<int> &v) {
  for (unsigned int n = 0; n < v.size(); n++) {
    v[n]++;
    std::cout << "v[" << n << "] = " << v[n] << std::endl;
  }
}

int main() {
  std::vector<int> v;
  v.push_back(42);
  v.push_back(-7);
  v.push_back(19);

  increment(v);

  for (unsigned int n = 0; n < v.size(); n++) {
    std::cout << "v[" << n << "] = " << v[n] << std::endl;
  }
  return 0;
}
```

Output:

```
$ ./vector13
v[0] = 43
v[1] = -6
v[2] = 20
v[0] = 43
v[1] = -6
v[2] = 20
$
```

## Tuple

* A tuple is another sequence object available in C++

* Tuples have fixed size established at the time of creation

* Elements in the tuple can be modified

* Elements need not be homogeneous, but the data types cannot be changed after
you create the tuple

### Tuple Example
- Don't forget to `#include <tuple>`

`src/tuple1.cpp`:

```c++
#include <iostream>
#include <string>
#include <tuple>

int main()
{
  std::string h = "Hello";
  int a = 42;

  auto t = std::make_tuple(h, a); //automatically put the type
  //auto t = std::make_tuple(h, a,b,v); //can add even more if you want


  std::cout << "t[0] = " << std::get<0>(t) << std::endl;
  std::cout << "t[1] = " << std::get<1>(t) << std::endl;

  std::get<1>(t) = 19;

  std::cout << "t[1] = " << std::get<1>(t) << std::endl;

  return 0;
}
```

Output:

```
$ g++ -std=c++11 -Wall -Wextra -Wconversion tuple1.cpp -o tuple1
$ ./tuple1
t[0] = Hello
t[1] = 42
t[1] = 19
$
```

### Vector of tuples
- Tuple library includes `emplace_back()` function, which allows you to append different variable types in a list instead of just one type

`src/tuple2.cpp`:

```c++
#include <iostream>
#include <fstream>
#include <tuple>
#include <vector>

int main() {
  std::ifstream f;
  std::vector<std::tuple<std::string,float,float,int>> names; //data has four columns (string,float,float,int). each item in the vector is this tuple type

  f.open("dist.female.first");
  if (f.is_open()) {
    std::string name;
    double perc1, perc2;
    int rank;
    while (f >> name >> perc1 >> perc2 >> rank) {
      names.emplace_back(name, perc1, perc2, rank);
    }
    f.close();
  }
  else {
    std::cerr << "ERROR: Failed to open file" << std::endl;
  }

  for(unsigned int n = 0; n < names.size(); n++) {
    std::cout << std::get<0>(names[n]) << " " << std::get<1>(names[n]) << std::endl;
  }

  return 0;
}
```

Output:

```
$ g++ -std=c++11 -Wall -Wextra -Wconversion -g -fsanitize=address    tuple2.cpp   -o tuple2
$ ./tuple2
MARY 2.629
PATRICIA 1.073
LINDA 1.035
BARBARA 0.98
ELIZABETH 0.937
JENNIFER 0.932
MARIA 0.828
SUSAN 0.794
MARGARET 0.768
DOROTHY 0.727
```

### Newer style iteration

`src/tuple3.cpp`:

```c++
#include <iostream>
#include <fstream>
#include <tuple>
#include <vector>

int main() {
  std::ifstream f;
  std::vector<std::tuple<int,int,int,int>> data;

  f.open("u.data");
  if (f.is_open()) {
    int uid, mid, rating, time;
    while (f >> uid >> mid >> rating >> time) {
      data.emplace_back(uid, mid, rating, time);
    //  data.push_back(std::tuple<uid, mid, rating, time>); // this is equivalent to above, but this is less efficient

    }
    f.close();
  }
  else {
    std::cerr << "ERROR: Failed to open file" << std::endl;
  }
// loops thru vector data and gives us one entry
  for (auto d : data) { // use auto so compiler automatically determines the type that's stored
    std::cout << std::get<0>(d) << " " << std::get<1>(d);
    std::cout << " " << std::get<2>(d) << std::endl;
  }

  return 0;
}
```

Output:

```
$ g++ -std=c++11 -Wall -Wextra -Wconversion -g -fsanitize=address    tuple3.cpp   -o tuple3
$ ./tuple3
196 242 3
186 302 3
22 377 1
244 51 2
166 346 1
298 474 4
115 265 2
253 465 5
305 451 3
6 86 3
```

## Reading

* **C++ Primer, Fifth Edition** by Lippman et al.

* Chapter 9: Sequential Containers: Sections 9.1 - 9.4
# CME 211: Lecture 20

Monday, November 9, 2015

Topics:

* C++ containers
* `map`
* `set`
* and more

## Container iteration

### Container iteration example 1

`src/iter1.cpp`:

```cpp
#include <iostream>
#include <vector>

int main()
{
  std::vector<double> vec;

  vec.push_back(7);
  vec.push_back(11);
  vec.push_back(42);

  for (auto v : vec)
    v++;

  for (auto v : vec)
    std::cout << v << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/iter1.cpp -o src/iter1
$ ./src/iter1
7
11
42
```

### Container iteration example 2

`src/iter2.cpp`:

```cpp
#include <iostream>
#include <vector>

int main()
{
  std::vector<double> vec;

  vec.push_back(7);
  vec.push_back(11);
  vec.push_back(42);

  for (auto &v : vec)
    v++;

  for (auto v : vec)
    std::cout << v << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/iter2.cpp -o src/iter2
$ ./src/iter2
8
12
43
```

## Map

* A C++ map is analogous to a dictionary in Python

* Need to specify data type for both the key and the value when instance is
declared

### Our first map

`src/map1.cpp`:

```cpp
#include <iostream>
#include <map>

int main()
{
  std::map<int,std::string> dir;

  dir[0] = std::string("north");
  dir[1] = std::string("east");
  dir[2] = std::string("south");
  dir[3] = std::string("west");

  std::cout << "dir[2] = " << dir[2] << std::endl;
  std::cout << "dir[0] = " << dir[0] << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/map1.cpp -o src/map1
$ ./src/map1
dir[2] = south
dir[0] = north
```

### Map iteration

`src/map2.cpp`:

```cpp
#include <iostream>
#include <map>

int main()
{
  std::map<int,std::string> dir;

  dir[0] = std::string("north");
  dir[1] = std::string("east");
  dir[2] = std::string("south");
  dir[3] = std::string("west");

  for (auto d : dir)
    std::cout << "d[" << d.first << "] = " << d.second << std::endl;

  for (auto &d : dir)
    std::cout << "d[" << d.first << "] = " << d.second << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/map2.cpp -o src/map2
$ ./src/map2
d[0] = north
d[1] = east
d[2] = south
d[3] = west
d[0] = north
d[1] = east
d[2] = south
d[3] = west
```

### Older style iteration

`src/map3.cpp`:

```cpp
#include <iostream>
#include <map>

int main()
{
  std::map<int,std::string> dir;

  dir[0] = std::string("north");
  dir[1] = std::string("east");
  dir[2] = std::string("south");
  dir[3] = std::string("west");

  for (std::map<int,std::string>::iterator i = dir.begin(); i != dir.end(); i++)
    std::cout << "d[" << i->first << "] = " << i->second << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/map3.cpp -o src/map3
$ ./src/map3
d[0] = north
d[1] = east
d[2] = south
d[3] = west
```

### Nonexistent keys

`src/map4.cpp`:

```cpp
#include <iostream>
#include <map>

int main()
{
  std::map<int,std::string> dir;

  dir[0] = std::string("north");
  dir[1] = std::string("east");
  dir[2] = std::string("south");
  dir[3] = std::string("west");

  std::cout << "dir.size() = " << dir.size() << std::endl;
  std::cout << "dir[5] = " << dir[5] << std::endl;
  std::cout << "dir.size() = " << dir.size() << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/map4.cpp -o src/map4
$ ./src/map4
dir.size() = 4
dir[5] =
dir.size() = 5
```

### Nonexistent keys

`src/map5.cpp`:

```cpp
#include <iostream>
#include <map>

int main()
{
  std::map<int,std::string> dir;

  dir[0] = std::string("north");
  dir[1] = std::string("east");
  dir[2] = std::string("south");
  dir[3] = std::string("west");

  std::cout << "dir.size() = " << dir.size() << std::endl;
  std::cout << "dir.at(5) = " << dir.at(5) << std::endl;
  std::cout << "dir.size() = " << dir.size() << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/map5.cpp -o src/map5
$ ./src/map5
dir.size() = 4
dir.at(5) =
libc++abi.dylib: terminating with uncaught exception of type std::out_of_range: map::at:  key not found
```

### Testing for a key

`src/map6.cpp`:

```cpp
#include <iostream>
#include <map>

int main()
{
  std::map<int,std::string> dir;

  dir[0] = std::string("north");
  dir[1] = std::string("east");
  dir[2] = std::string("south");
  dir[3] = std::string("west");

  std::cout << "dir.count(2) = " << dir.count(2) << std::endl;
  std::cout << "dir.count(5) = " << dir.count(5) << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/map6.cpp -o src/map6
$ ./src/map6
dir.count(2) = 1
dir.count(5) = 0
```

### Testing for a key

`src/map7.cpp`:

```cpp
#include <iostream>
#include <map>

int main() {
  std::map<int,std::string> dir;

  dir[0] = std::string("north");
  dir[1] = std::string("east");
  dir[2] = std::string("south");
  dir[3] = std::string("west");

  int key = 2;
  auto iter = dir.find(key);
  if (iter == dir.end()) {
    std::cout << "key " << key << " is not present" << std::endl;
  }
  else {
    std::cout << "key " << key << " is present" << std::endl;
    std::cout << "value is " << iter->second << std::endl;
  }

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/map7.cpp -o src/map7
$ ./src/map7
key 2 is present
value is south
```

### Key order

`src/map8.cpp`:

```cpp
#include <iostream>
#include <map>

int main()
{
  std::map<int,std::string> dir;

  dir[2] = std::string("south");
  dir[3] = std::string("west");
  dir[1] = std::string("east");
  dir[0] = std::string("north");

  for (auto &d : dir)
    std::cout << d.first << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/map8.cpp -o src/map8
$ ./src/map8
0
1
2
3
```

### Map and tuples

`src/map9.cpp`:

```cpp
#include <fstream>
#include <iostream>
#include <map>
#include <string>
#include <tuple>

int main() {
  std::ifstream f("dist.female.first");
  if (not f.good()) {
    std::cerr << "ERROR: Failed to open file" << std::endl;
    return 1;
  }

  std::map<std::string,std::tuple<double,double,int>> names;

  std::string name;
  double perc1, perc2;
  int rank;
  while(f >> name >> perc1 >> perc2 >> rank) {
    names[name] = std::make_tuple(perc1, perc2, rank);
  }

  for(auto &data : names) {
    std::cout << data.first << " " << std::get<0>(data.second) << std::endl;
  }

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/map9.cpp -o src/map9
$ ./src/map9
BARBARA 0.98
DOROTHY 0.727
ELIZABETH 0.937
JENNIFER 0.932
LINDA 1.035
MARGARET 0.768
MARIA 0.828
MARY 2.629
PATRICIA 1.073
SUSAN 0.794
```

### Using functions

`src/readnames.hpp`:

```cpp
#ifndef READNAMES_HPP
#define READNAMES_HPP

#include <map>
#include <string>
#include <tuple>

std::map<std::string,std::tuple<double,double,int>> ReadNames(std::string filename);

#endif /* READNAMES_HPP */
```

`src/readnames.cpp`:

```cpp
#include <fstream>
#include <iostream>

#include "readnames.hpp"

std::map<std::string,std::tuple<double,double,int>> ReadNames(std::string filename)
{
  std::ifstream f(filename);

  std::map<std::string,std::tuple<double,double,int>> names;

  std::string name;
  double perc1, perc2;
  int rank;
  while(f >> name >> perc1 >> perc2 >> rank) {
    names[name] = std::make_tuple(perc1, perc2, rank);
  }

  return names;
}
```

`#pragma once`: only include this file once (not standard)

`src/testname.hpp`:

```cpp
#pragma once

#include <map>
#include <string>
#include <tuple>

double TestName(std::map<std::string,std::tuple<double,double,int>> names,
                std::string name);
```

`src/testname.cpp`:

```cpp
#include <iostream>

#include "testname.hpp"

double TestName(std::map<std::string,std::tuple<double,double,int>> names,
                std::string name)
{
  double percentage = 0.;

  auto match = names.find(name);
  if (match != names.end())
  {
    percentage = std::get<0>(match->second);
  }

  return percentage;
}
```

### Using functions

`src/main.cpp`:

```cpp
#include <iostream>
#include <string>
#include <vector>

#include "readnames.hpp"
#include "testname.hpp"

int main()
{
  auto names = ReadNames("dist.female.first");

  std::vector<std::string> tests;
  tests.push_back("LINDA");
  tests.push_back("PETER");
  tests.push_back("DOROTHY");

  for(auto test : tests)
  {
    std::cout << test << " " << TestName(names, test) << std::endl;
  }

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/main.cpp src/readnames.cpp src/testname.cpp -o src/main
$ ./src/main
LINDA 1.035
PETER 0
DOROTHY 0.727
```

### Sets

`src/set.cpp`:

```cpp
#include <algorithm>
#include <fstream>
#include <iostream>
#include <set>
#include <string>

std::set<std::string> ReadNames(std::string filename)
{
  std::set<std::string> names;

  std::ifstream f(filename);
  if (not f.is_open())
  {
    std::cerr << "ERROR: Could not read file " << filename << std::endl;
    return names;
  }

  std::string name;
  double perc1, perc2;
  int rank;
  while (f >> name >> perc1 >> perc2 >> rank)
  {
    names.insert(name);
  }
  f.close();

  return names;
}

int main()
{
  auto fnames = ReadNames("dist.female.first");
  auto mnames = ReadNames("dist.male.first");

  std::set<std::string> common;

  std::set_intersection(fnames.begin(), fnames.end(), mnames.begin(), mnames.end(),
                        std::inserter(common, common.begin()));

  std::cout << fnames.size() << " female names" << std::endl;
  std::cout << mnames.size() << " male names" << std::endl;
  std::cout << common.size() << " common names" << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/set.cpp -o src/set
$ ./src/set
ERROR: Could not read file dist.male.first
10 female names
0 male names
0 common names
```

### Additional data structures

* `std::array` (C++ 2011)

* `std::list`

* `std::forward_list` (C++ 2011)

* `std::unordered_map` (C++ 2011)

* `std::unordered_set` (C++ 2011)

### Array example

`src/array.cpp`:

```cpp
#include <array>
#include <iostream>

int main()
{
  std::array<double,4> a;

  a.fill(1.);
  a[2] = 3.;

  for (auto val : a)
    std::cout << val << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/array.cpp -o src/array
$ ./src/array
1
1
3
1
```

## Linked lists

* Ordered data sequence similar to a C++ vector or Python list, but data is not
stored contiguously

* Sense of order is maintained via links

* There is additional storage overhead for the links

* But this allows for insertion and removal operations in constant time

![fig](lecture-20/fig/linked-list.png)

### List example

`src/list.cpp`:

```cpp
#include <iostream>
#include <list>

int main()
{
  std::list<int> l;
  l.push_back(42);
  l.push_back(17);
  l.push_back(9);

  auto it = l.begin();
  advance(it, 1);
  l.erase(it);

  for (auto val : l)
    std::cout << val << std::endl;

  return 0;
}
```

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/list.cpp -o src/list
$ ./src/list
42
9
```

### Maps and sets

* Python dictionaries and sets are internally implemented by using hashing

* For hashing implementation, time complexity for data access is (amortized)
constant time

* Instances of C++ `std::map` and `std::set` are internally implemented using a tree
data structure

* For a tree, time complexity for data access is `O(log n)`

* Reference: <http://www.cplusplus.com/reference/map/map/operator[]/>

### Unordered maps and sets

* In the C++ 2011 standard the `std::unordered_map` and `set::unordered_set`
were added

* Like Python, internal implementation is based on hashing

* Faster access, but entries are no longer ordered (but that usually doesn't
  matter)

### Unordered map example

`src/unordered_map.cpp`:

```cpp
#include <iostream>
#include <unordered_map>

int main()
{
  std::unordered_map<int,std::string> dir;

  dir[0] = std::string("north");
  dir[1] = std::string("east");
  dir[2] = std::string("south");
  dir[3] = std::string("west");

  std::cout << "dir[2] = " << dir[2] << std::endl;
  std::cout << "dir[0] = " << dir[0] << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/unordered_map.cpp -o src/unordered_map
$ ./src/unordered_map
dir[2] = south
dir[0] = north
```

## Reading

* **C++ Primer, Fifth Edition** by Lippman et al.

* Chapter 11: Associative Containers: Sections 11.1 - 11.3
# CME 211: Lecture 21

Wednesday, November 11, 2015

Topics:

* Multi-dimensional data
* Boost `multi_array`

## Layout in memory for `vector`

![fig](lecture-21/fig/vector-memory.png)

* Memory for `std::vector` has 2 parts:

  * Memory for the vector data

  * Memory for the `std::vector` container.  This part (essentially) includes
  the memory address of the vector data, the size of the vector and capacity.

* The 2 parts may be very far apart in the memory address space.

### Look at the details

`src/vector_memory.cpp`:

```cpp
#include <iostream>
#include <vector>

int main() {
  std::vector<int> a;
  for (int i = 0; i < 10; i++) {
    a.push_back(i); //requires 40 bytes of memory
  }
  std::cout << "sizeof(a): " << sizeof(a) << std::endl; //sizeof() gives size in bytes required by that variable and the type (for example, this returns n bytes required by the vector container)
  std::cout << "    memory location of a: " << &a << std::endl; //&a gives address of the object a (orange thing in the pic)
  std::cout << " memory location of data: " << a.data() << std::endl; //.data() returns memory location of data (blue thing in pic)
  std::cout << "difference in memory loc: "
            << double((int*)&a-a.data()) / 1024 / 1024 / 1024 // how far apart the memory and its container (address of a-address of data)
            << " GB" << std::endl; //this returns in Gigabytes instead of bytes
  return 0;
}
```

Output:

Note that memory allocation is stochastic- this is for security purposes, making it harder for ppl to introduce malicious codes

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/vector_memory.cpp -o src/vector_memory
$ ./src/vector_memory
sizeof(a): 24
    memory location of a: 0x7fff541738e0 //hexadecimals
 memory location of data: 0x7f9d4b500040
difference in memory loc: 98.0343 GB //difference in address space, not physical memory space
$ ./src/vector_memory
sizeof(a): 24
    memory location of a: 0x7fff5d1498e0
 memory location of data: 0x7ffddac04ad0
difference in memory loc: 1.5091 GB
$ ./src/vector_memory
sizeof(a): 24
    memory location of a: 0x7fff5b5a88e0
 memory location of data: 0x7ffcc2c04ad0
difference in memory loc: 2.5961 GB
```

* The size of the `std::vector` container is 24 bytes, this could be for

  * 8 bytes for the memory address of the vector data

  * 8 bytes for the size of the vector, number of elements stored

  * 8 bytes of the capacity of the vector, number of elements that may be stored
  before reallocation

* Memory locations are different in each run of the program.  This is a security
  feature to make it harder to introduce malicious code or data.

## Multidimensional data

* How do we handle multidimensional data in C++?

### Container of containers
This is basically a vector of vectors (causes things in different rows to be NOT contiguous in memory)
`src/multi1.cpp`:

```cpp
#include <vector>
#include <iostream>

int main() {
  // declare vector of vectors
  std::vector< std::vector<double> > v;
  // add empty "second-level" vectors
  v.push_back(std::vector<double>());
  v.push_back(std::vector<double>());
  v.push_back(std::vector<double>());
  // add some data
  double n = 0.;
  for(unsigned int i = 0; i < 3; i++) {
    for(unsigned int j = 0; j < 3; j++) {
      v[i].push_back(n);
      n++;
    }
  }
  // print
  for(unsigned int i = 0; i < 3; i++) {
    for(unsigned int j = 0; j < 3; j++) {
      std::cout << "v[" << i << "][" << j << "] = " << v[i][j] << std::endl;
    }
  }
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/multi1.cpp -o src/multi1
$ ./src/multi1
v[0][0] = 0
v[0][1] = 1
v[0][2] = 2
v[1][0] = 3
v[1][1] = 4
v[1][2] = 5
v[2][0] = 6
v[2][1] = 7
v[2][2] = 8
```

### Layout in memory

![fig](lecture-21/fig/vector-of-vectors.png)

### Contiguous memory
Source of programmer error (getting something wrong in the indexing)
Should write some helper functions to make indexing a bit nicer

`src/multi2.cpp`:

```cpp
#include <iostream>
#include <vector>

int main() {
  unsigned int nrows = 3, ncols = 3;
  std::vector<double> a;
  a.resize(nrows*ncols); //data for entire array is contiguous in memory

  double n = 0.;
  for(unsigned int i = 0; i < nrows; i++) {
    for(unsigned int j = 0; j < ncols; j++) {
      // manual indexing into "multi-dimensional array"
      a[i*ncols + j] = n;
      n++;
    }
  }

  for(unsigned int i = 0; i < nrows*ncols; i++) {
    std::cout << "a[" << i << "] = " << a[i] << std::endl;
  }
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/multi2.cpp -o src/multi2
$ ./src/multi2
a[0] = 0
a[1] = 1
a[2] = 2
a[3] = 3
a[4] = 4
a[5] = 5
a[6] = 6
a[7] = 7
a[8] = 8
```

## Boost Multidimensional Array Library
Check boost.org for a ton of different C++ libraries


`src/array1.cpp`:

```cpp
#include <iostream>
#include <boost/multi_array.hpp>

int main() {
  unsigned int nrows = 3, ncols = 3;
  boost::multi_array<double, 2> a(boost::extents[nrows][ncols]); //boost:: namespace; double is the type we want to store in the array, '2' is the number of dimensions you want the array to have

  double n = 0.;
  for (unsigned int i = 0; i < nrows; i++) {
    for (unsigned int j = 0; j < ncols; j++) {
      a[i][j] = n; // access elements like static array
      n++;
    }
  }

  for (unsigned int i = 0; i < nrows; i++) {
    for (unsigned int j = 0; j < ncols; j++) {
      std::cout << "a[" << i << "][" << j << "] = " << a[i][j] << std::endl;
    }
  }
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/array1.cpp -o src/array1
$ ./src/array1
a[0][0] = 0
a[0][1] = 1
a[0][2] = 2
a[1][0] = 3
a[1][1] = 4
a[1][2] = 5
a[2][0] = 6
a[2][1] = 7
a[2][2] = 8
```

### Accessing the contiguous memory
Use a.data() for multiarray object- you can index into that to access data items sequentially (linearly)
Second index is contiguous (j) and there will be a stride in (i)


`src/array2.cpp`:

```cpp
#include <iostream>

#include <boost/multi_array.hpp>

int main() {
  boost::multi_array<double, 2> a(boost::extents[3][3]);

  double n = 0.;
  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      a[i][j] = n;
      n++;
    }
  }

  for (unsigned int n = 0; n < a.num_elements(); n++) {
    std::cout << "a.data()[" << n << "] = " << a.data()[n] << std::endl;
  }

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/array2.cpp -o src/array2
$ ./src/array2
a.data()[0] = 0
a.data()[1] = 1
a.data()[2] = 2
a.data()[3] = 3
a.data()[4] = 4
a.data()[5] = 5
a.data()[6] = 6
a.data()[7] = 7
a.data()[8] = 8
```

### Performance

`src/perf1.cpp`:

```cpp
#include <iostream>
#include <ctime>
#include <boost/multi_array.hpp>

int main() {
  unsigned int nrows = 8192, ncols = 8192;
  boost::multi_array<double, 2> a(boost::extents[nrows][ncols]);

  for (unsigned int i = 0; i < nrows; i++) {
    for (unsigned int j = 0; j < ncols; j++) {
      a[i][j] = 1.0;
    }
  }

  auto t0 = std::clock();
  double sum = 0.;
  for (unsigned int i = 0; i < nrows; i++) {
    for (unsigned int j = 0; j < ncols; j++) {
      sum += a[i][j];
    }
  }
  auto t1 = std::clock();

  std::cout << " boost: sum = " << sum << ", time = "
            << double(t1-t0) / CLOCKS_PER_SEC
            << " seconds"<< std::endl;

  auto b = a.data(); //access memory linearly
  t0 = std::clock();
  sum = 0.;
  for (unsigned int n = 0; n < nrows*ncols; n++) {
    sum += b[n];
  }
  t1 = std::clock();
  std::cout << "direct: sum = " << sum << ", time = "
            << double(t1-t0) / CLOCKS_PER_SEC
            << " seconds"<< std::endl;
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/perf1.cpp -o src/perf1
$ ./src/perf1
 boost: sum = 6.71089e+07, time = 4.4801 seconds
direct: sum = 6.71089e+07, time = 0.1854 seconds
$ ./src/perf1
 boost: sum = 6.71089e+07, time = 4.4333 seconds
direct: sum = 6.71089e+07, time = 0.186353 seconds
$ ./src/perf1
 boost: sum = 6.71089e+07, time = 4.39778 seconds
direct: sum = 6.71089e+07, time = 0.184639 seconds
```
Why? Boost does range checks if you're out of bounds. We can disable these (see below)


### Performance

From `src/perf2.cpp`:

```c++
// disable boost range checking
#define BOOST_DISABLE_ASSERTS
#include <boost/multi_array.hpp>
```
Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/perf2.cpp -o src/perf2
$ ./src/perf2
 boost: sum = 6.71089e+07, time = 3.97609 seconds
direct: sum = 6.71089e+07, time = 0.184989 seconds
$ ./src/perf2
 boost: sum = 6.71089e+07, time = 3.97695 seconds
direct: sum = 6.71089e+07, time = 0.18614 seconds
$ ./src/perf2
 boost: sum = 6.71089e+07, time = 3.97976 seconds
direct: sum = 6.71089e+07, time = 0.184732 seconds
```
We're still quite a bit slower than the standard linear run of the data even when we disable the range checks

### Compiler optimization

Enable compiler optimizations with the `-O3` argument.
Boost is still a bit slower, but not bad!
With range checking:

Output:

```
$ clang++ -O3 -std=c++11 -Wall -Wextra -Wconversion src/perf1.cpp -o src/perf1
$ ./src/perf1
 boost: sum = 6.71089e+07, time = 0.064458 seconds
direct: sum = 6.71089e+07, time = 0.064259 seconds
$ ./src/perf1
 boost: sum = 6.71089e+07, time = 0.068965 seconds
direct: sum = 6.71089e+07, time = 0.064744 seconds
$ ./src/perf1
 boost: sum = 6.71089e+07, time = 0.066465 seconds
direct: sum = 6.71089e+07, time = 0.062854 seconds
```

Range checking disabled:
Output:
Really good- compiler did an amazing job optimizing the code so that our memory access is as efficient as linear access
Drawbacks: Compilation phase takes longer, you may lose something else in arithmetic or other things (check up manual for more details)
```
$ clang++ -O3 -std=c++11 -Wall -Wextra -Wconversion src/perf2.cpp -o src/perf2
$ ./src/perf2
 boost: sum = 6.71089e+07, time = 0.065588 seconds
direct: sum = 6.71089e+07, time = 0.067835 seconds
$ ./src/perf2
 boost: sum = 6.71089e+07, time = 0.065887 seconds
direct: sum = 6.71089e+07, time = 0.071683 seconds
$ ./src/perf2
 boost: sum = 6.71089e+07, time = 0.064704 seconds
direct: sum = 6.71089e+07, time = 0.062087 seconds
```

### Range checking

`src/array3a.cpp`:

```cpp
#include <iostream>
#include <boost/multi_array.hpp>

int main() {
  boost::multi_array<double, 2> a(boost::extents[3][3]);
  a[3][3] = 1.; //try putting value out of bounds
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/array3a.cpp -o src/array3a
$ ./src/array3a
Assertion failed: (size_type(idx - index_bases[0]) < extents[0]), function access, file /usr/local/include/boost/multi_array/base.hpp, line 136.
```

### Range checking

`src/array3b.cpp`:

```cpp
#include <iostream>
#define BOOST_DISABLE_ASSERTS
#include <boost/multi_array.hpp>

int main() {
  boost::multi_array<double, 2> a(boost::extents[3][3]);
  a[3][3] = 1.;
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/array3b.cpp -o src/array3b
$ ./src/array3b
$ clang++ -std=c++11 -g -fsanitize=address -Wall -Wextra -Wconversion src/array3b.cpp -o src/array3b
$ ./src/array3b
...blah blah...
```

### Range checking

Another method to check for memory leaks is `valgrind`.

Still leave in -g debugging flag
execute valgrind <give program you want to execute>
able to detect out of bounds memory access

Output:

```
$ clang++ -g -Wall -Wextra -Wconversion src/array3b.cpp -o src/array3b
$ valgrind ./src/array3b
==22635== Memcheck, a memory error detector
...blah blah...
```

### Elementwise comparison

`src/array5.cpp`:

```cpp
#include <iostream>
#include <boost/multi_array.hpp>

int main() {
  boost::multi_array<double, 2> a(boost::extents[3][3]);
  boost::multi_array<double, 2> b(boost::extents[3][3]);

  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      a[i][j] = 1.;
      b[i][j] = 2.;
    }
  }

  std::cout << "a == b: " << (a == b) << std::endl; //returns true if all elements of a and b are equal
  std::cout << "a < b: " << (a < b) << std::endl;
  std::cout << "a > b: " << (a > b) << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/array5.cpp -o src/array5
$ ./src/array5
a == b: 0
a < b: 1
a > b: 0
```

### COPYING
- the assignment operator = is COPYING the array
`src/array6a.cpp`:

```cpp
#include <iostream>
#include <boost/multi_array.hpp>

int main() {
  boost::multi_array<double, 2> a(boost::extents[3][3]);

  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      a[i][j] = 1.;
    }
  }

  auto b = a; // copy or reference?

//modify elements of a
  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      a[i][j] = 2.;
    }
  }
//print out both a and b
  std::cout << "a b" << std::endl;
  std::cout << "---" << std::endl;
  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      std::cout << a[i][j] << " " << b[i][j] << std::endl;
    }
  }
  return 0;
}
```

Output:
```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/array6a.cpp -o src/array6a
$ ./src/array6a
a b
---
2 1
2 1
2 1
2 1
2 1
2 1
2 1
2 1
2 1
```

### Passing an array to a function: Passes by COPY

This convention is pass by COPY

`src/array6b.cpp`:

```cpp
#include <iostream>
#include <boost/multi_array.hpp>

void increment(boost::multi_array<double, 2> b) {
  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      b[i][j]++;
    }
  }
}

int main() {
  boost::multi_array<double, 2> a(boost::extents[3][3]);

  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      a[i][j] = 1.;
    }
  }

  increment(a);

  std::cout << "After increment function" << std::endl;

  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      std::cout << a[i][j] << std::endl;
    }
  }
  return 0;
}
```

Output:

```
kmwang14@corn20:~/CME211/Cplusplus/lecture-21$ g++ -std=c++11 -Wall -Wextra -Wconversion array6b.cpp
kmwang14@corn20:~/CME211/Cplusplus/lecture-21$ ./a.out
After increment function
1
1
1
1
1
1
1
1
1

```

### Passing a boost multi_array by REFERENCE

Specify by the & (reference to the data type)

From `src/array6c.cpp`:

```cpp
void increment(boost::multi_array<double, 2>& b) {
  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      b[i][j]++;
    }
  }
}
```

Output:

```
kmwang14@corn20:~/CME211/Cplusplus/lecture-21$ g++ -std=c++11 -Wall -Wextra -Wconversion array6c.cpp
kmwang14@corn20:~/CME211/Cplusplus/lecture-21$ ./a.out
After increment function
2
2
2
2
2
2
2
2
2
```

### Array operations?

* Boost `multi_array` does not support array operations like NumPy

* If `a` is a `multi_array` things like `2*a` and `a = 1.0` will not work and
will lead to very long compiler error messages.

* If you want this kind of stuff, have a look at:

  * http://eigen.tuxfamily.org/index.php?title=Main_Page

  * http://arma.sourceforge.net/

### Array views

An **array view** is essentially a reference into a sub-array of a larger array.

`src/array9.cpp`:

```cpp
#include <iostream>
#include <boost/multi_array.hpp>

int main() {
  boost::multi_array<double, 2> a(boost::extents[3][3]);

  double n = 0.;
  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      a[i][j] = n;
      n++;
    }
  }

  /* Setup b as a view into a subset of a. */
  typedef boost::multi_array<double, 2>::index_range index_range; // rename this long type (boost::multi_array<double, 2>::index_range) and call it index_range instead, similar to import numpy as np in python- it's just an alias for the name
  auto b = a[boost::indices[index_range(1,3)][index_range(1,3)]];

  for (unsigned int i = 0; i < 2; i++) {
    for (unsigned int j = 0; j < 2; j++) {
      b[i][j] = -1.;
    }
  }

  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      std::cout << a[i][j] << std::endl;
    }
  }
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/array9.cpp -o src/array9
$ ./src/array9
0
1
2
3
-1
-1
6
-1
-1
```

### Storage order

`src/array10a.cpp`:

```cpp
#include <iostream>
#include <boost/multi_array.hpp>

int main() {
  boost::multi_array<double, 2> a(boost::extents[3][3]);

  double n = 0.;
  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      a[i][j] = n; //rightmost index is contiguous in memory
      n++;
    }
  }

  auto b = a.data();
  for (unsigned int n = 0; n < a.num_elements(); n++) {
    std::cout << "b[" << n << "] = " << b[n] << std::endl;
  }

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/array10a.cpp -o src/array10a
$ ./src/array10a
b[0] = 0
b[1] = 1
b[2] = 2
b[3] = 3
b[4] = 4
b[5] = 5
b[6] = 6
b[7] = 7
b[8] = 8
```

* Uses C convention that rows are stored contiguously in memory (row major
order)

* Or put another way, the last index in a multidimensional array changes fastest
when traversing through linear memory

### "Fortran" storage order

Is the opposite: the first index changes fastest and is contigous in memory

`src/array10b.cpp`:

```cpp
#include <iostream>
#include <boost/multi_array.hpp>

int main() {
  boost::multi_array<double, 2> a(boost::extents[3][3],
                                  boost::fortran_storage_order());

  double n = 0.;
  for (unsigned int i = 0; i < 3; i++) {
    for (unsigned int j = 0; j < 3; j++) {
      a[i][j] = n;
      n++;
    }
  }

  auto b = a.data();
  for (unsigned int n = 0; n < a.num_elements(); n++) {
    std::cout << "b[" << n << "] = " << b[n] << std::endl;
  }

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/array10b.cpp -o src/array10b
$ ./src/array10b
b[0] = 0
b[1] = 3
b[2] = 6
b[3] = 1
b[4] = 4
b[5] = 7
b[6] = 2
b[7] = 5
b[8] = 8
```

* In Fortran columns are stored contiguously in memory (column major order)

* Or put another way, the first index in a multidimensional array changes
fastest when traversing through linear memory

### MultiArrays are containers

From `src/accumulate.cpp`:

```cpp
  for (unsigned int i = 0; i < nrows; i++) {
    sum += std::accumulate(a[i].begin(), a[i].end(), 0.);
  }
```

Output:
Can optimize all of the using the -03
```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/accumulate.cpp -o src/accumulate
$ ./src/accumulate
 boost: sum = 6.71089e+07, time = 4.78976 seconds //array access is slow
direct: sum = 6.71089e+07, time = 0.186091 seconds //going over memory is fast
 accum: sum = 6.71089e+07, time = 2.63903 seconds //somewhere in the middle
```

### Boost summary

From <http://www.boost.org>:

Boost provides free peer-reviewed portable C++ source libraries.

We emphasize libraries that work well with the C++ Standard Library. Boost
libraries are intended to be widely useful, and usable across a broad spectrum
of applications. The Boost license encourages both commercial and non-commercial
use.

**Good**:

* Well implemented library with a lot of diverse functionality.

* Approximately 115 sub-libraries, of which MultiArray is just
one of them.

* Cross platform (Windows, Mac, Linux) and friendly license for
commercial applications.

**Bad**:

* Sometimes the documentation can be a bit lacking.

* Not a standard part of C++ (external dependency).

* Some people seem to have a real aversion to it.

* Sometimes the `boost` library authors make an effort to utilize C++ features
  at the expense of code clarity.  I believe this is why some people have strong
  feelings against `boost`.

**Practical advice**:

* Use boost if it helps you get your work done quickly.

* If you find yourself trying too hard to fit into a particular boost library,
then maybe look for something else.

* It is sometimes nice to have single external dependency that contains many useful
  utilities as opposed to many smaller external dependencies.

### Challenge problem: how to do exp() function using python?
Hint: use the taylor series expansion around 0
Plot the error versus n based on how many terms you include/truncate

# CME 211: Lecture 22

Friday, November 13, 2015

Topics:

* Compilation process
* Make for building software

## Compilation

* Although you can go from source code to an executable in one command, the
process is actually made up of 4 steps

  * Preprocessing

  * Compilation

  * Assembly

  * Linking

* `g++` and `clang++` (and `gcc` or `clang` for C code) are driver programs that
invoke the appropriate tools to perform these steps

* This is a high level overview.  The compilation process also includes
  optimization phases during compilation and linking.

### Libraries

* Libraries are really just a file that contain one or more `.o` files

* On Linux these files typically have a `.a` (static library) or `.so` (dynamic
library) extension (shared object 'so')

* `.so` files are analogous to `.dll` files on Windows

* `.dylib` files on Mac OS X and iOS are also very similar to `.so` files

* Static libraries are factored into the executable at link time in the
compilation process.

* Shared (dynamic) libraries are loaded up at run time.


### Make
* Automation tool for expressing how your C/C++/Fortran code should be compiled

* Good for small projects

* But be careful with dependencies.  It is **very** important to understand this
  process for larger projects.

* Some people would not recommend hand writing Makefile(s) for larger projects
(use CMake or similar)

* With discipline, I believe that Make is a good tool for large projects.  This
  is what I use.  Sometimes CMake and other tools make it harder to build
  projects.

# CME 211: Lecture 23

Monday, November 16, 2015

Topic: C++ Object Oriented Programming

## A simple class

`src/class1.cpp`:

```c++
#include <string>

class user {
  // data members (members of the types)
  int id;
  std::string name;
}; //REQUIRED SEMICOLON!!

int main()
{
  user u; // object (instance of the class)
  return 0; //creates object in memory, doesn't do anything
}
```

Output:

```
$ clang++ -Wall -Wextra -Wconversion src/class1.cpp -o src/class1
src/class1.cpp:5:7: warning: private field 'id' is not used [-Wunused-private-field]
  int id;
      ^
1 warning generated.
$ ./src/class1
```

### Member access

`src/class2.cpp`:

```c++
#include <iostream>
#include <string>

class user
{
  int id;
  std::string name;
};

int main()
{
  user u; //create object u of class user
  u.id = 7; // Member access via dot notation
  std::cout << "u.id = " << u.id << std::endl;
  return 0; //code does NOT compile- everything is private; cannot access data inside class
}
```

Output:

```
$ clang++ -Wall -Wextra -Wconversion src/class2.cpp -o src/class2
src/class2.cpp:13:5: error: 'id' is a private member of 'user'
  u.id = 7; // Member access via dot notation
    ^
src/class2.cpp:6:7: note: implicitly declared private here
  int id;
      ^
src/class2.cpp:14:31: error: 'id' is a private member of 'user'
  std::cout << "u.id = " << u.id << std::endl;
                              ^
src/class2.cpp:6:7: note: implicitly declared private here
  int id;
      ^
2 errors generated.
```

### Member access

`src/struct1.cpp`:

```c++
#include <iostream>
#include <string>

struct user //default access is public
{
  int id;
  std::string name;
};

int main()
{
  user u;
  u.id = 7; //similar to python code we've seen before
  std::cout << "u.id = " << u.id << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -Wall -Wextra -Wconversion src/struct1.cpp -o src/struct1
$ ./src/struct1
u.id = 7
```

### Member access

* C++ is strict about member access

* Need to know about default behavior

* And how to override defaults via access specifiers

### Access specifiers

* `private`: data or method member only accessible from within member(s) of the
same class

* `public`: data or method member accessible by anyone using dot notation

* Default access specifier for `class` is `private`

* Default access specifier for `struct` is `public`

### Overriding default access

`src/class3.cpp`:

```c++
#include <iostream>
#include <string>

class user {
 public: // everything after this will be public
  int id;
  std::string name;
};

int main() {
  user u;
  u.id = 7;
  u.name = "Leland";
  std::cout << "u.id = " << u.id << std::endl;
  std::cout << "u.name = " << u.name << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -Wall -Wextra -Wconversion src/class3.cpp -o src/class3
$ ./src/class3
u.id = 7
u.name = Leland
```

### Overriding default access

`src/struct2.cpp`:

```c++
#include <iostream>
#include <string>

struct user {
  int id;
 private: // everything after this will be private
  std::string name;
};

int main() {
  user u;
  u.id = 7;
  u.name = "Leland";
  std::cout << "u.id = " << u.id << std::endl;
  std::cout << "u.name = " << u.name << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -Wall -Wextra -Wconversion src/struct2.cpp -o src/struct2
src/struct2.cpp:13:5: error: 'name' is a private member of 'user'
  u.name = "Leland";
    ^
src/struct2.cpp:7:15: note: declared private here
  std::string name;
              ^
src/struct2.cpp:15:33: error: 'name' is a private member of 'user'
  std::cout << "u.name = " << u.name << std::endl;
                                ^
src/struct2.cpp:7:15: note: declared private here
  std::string name;
              ^
2 errors generated.
```

### Mix and match

`src/class4.cpp`:

```c++
#include <iostream>
#include <string>

class user {
  int id;
 public:
  std::string name;
 private:
  int age;
};

int main() {
  user u;
  u.id = 7; //this is private (will give compile time errors)
  u.name = "Leland";
  u.age = 12; //this is private

  std::cout << "u.id = " << u.id << std::endl;
  std::cout << "u.name = " << u.name << std::endl;
  std::cout << "u.age = " << u.age << std::endl;

  return 0;
}
```

Output:

```
$ clang++ -Wall -Wextra -Wconversion src/class4.cpp -o src/class4
src/class4.cpp:14:5: error: 'id' is a private member of 'user'
  u.id = 7;
    ^
src/class4.cpp:5:7: note: implicitly declared private here
  int id;
      ^
src/class4.cpp:16:5: error: 'age' is a private member of 'user'
  u.age = 12;
    ^
src/class4.cpp:9:7: note: declared private here
  int age;
      ^
src/class4.cpp:18:31: error: 'id' is a private member of 'user'
  std::cout << "u.id = " << u.id << std::endl;
                              ^
src/class4.cpp:5:7: note: implicitly declared private here
  int id;
      ^
src/class4.cpp:20:32: error: 'age' is a private member of 'user'
  std::cout << "u.age = " << u.age << std::endl;
                               ^
src/class4.cpp:9:7: note: declared private here
  int age;
      ^
4 errors generated.
```

### "Adding" a member

`src/struct3.cpp`:

```c++
#include <iostream>

struct user {
  int id;
};

int main() {
  user u;
  u.id = 7;
  u.age = 12; //age wasn't declared in struct user
  return 0;
}
```

Output:

```
$ clang++ -Wall -Wextra -Wconversion src/struct3.cpp -o src/struct3
src/struct3.cpp:10:5: error: no member named 'age' in 'user'
  u.age = 12;
  ~ ^
1 error generated.
```

### Our first method

`src/class5.cpp`:

```c++
#include <iostream>

class user {
  // data member initialization is a C++11 feature
  int id = 7; //data member initialization
  int getId(void) {//define a function inside a class (note:don't need self)
    return id;
  }
};

int main() {
  user u;
  std::cout << "u.getId() = " << u.getId() << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/class5.cpp -o src/class5
src/class5.cpp:13:36: error: 'getId' is a private member of 'user'
  std::cout << "u.getId() = " << u.getId() << std::endl;
                                   ^
src/class5.cpp:6:7: note: implicitly declared private here
  int getId(void) {
      ^
1 error generated.
```

### Our first method

`src/class6.cpp`:

```c++
#include <iostream>

class user {
  int id = 7; //this is private, user cannot modify
 public:
  int getId(void) { //getter method (read only method for that variable)
    return id;
  }
};

int main() {
  user u;
  std::cout << "u.getId() = " << u.getId() << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/class6.cpp -o src/class6
$ ./src/class6
u.getId() = 7
```

### Naming

`src/class7.cpp`:

```c++
#include <iostream>

class user {
  int id = 1;
 public:
  int getId(void) { return id; }
  void setId(int id) { id = id; } //user can set it, but id = id clashes with int id (this is ambiguous). id refers to id from argument, and this causes errors
};

int main() {
  user u;
  u.setId(7);
  std::cout << "u.getId() = " << u.getId() << std::endl;
  u.setId(42);
  std::cout << "u.getId() = " << u.getId() << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/class7.cpp -o src/class7
src/class7.cpp:7:27: warning: explicitly assigning value of variable of type 'int' to itself [-Wself-assign]
  void setId(int id) { id = id; }
                       ~~ ^ ~~
1 warning generated.
$ ./src/class7
u.getId() = 1
u.getId() = 1
```

### One solution

google.github style guide

`src/class8.cpp`:

```c++
#include <iostream>

class user {
  int id = 1;
 public:
  int getId(void) { return id; }
  void setId(int id_) { id = id_; } // id_ refers to argument
};

int main()
{
  user u;
  u.setId(7);
  std::cout << "u.getId() = " << u.getId() << std::endl;
  u.setId(42);
  std::cout << "u.getId() = " << u.getId() << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/class8.cpp -o src/class8
$ ./src/class8
u.getId() = 7
u.getId() = 42
```

### `this`

`src/class9.cpp`:

```c++
#include <iostream>

class user {
  int id = 1;
 public:
  int getId(void) { return id; }
  void setId(int id) { this->id = id; } //this is analogous to self in python; "this" is the pointer to the object. when we want to access data id, this-> get acces to data member
};

int main() {
  user u;
  u.setId(7);
  std::cout << "u.getId() = " << u.getId() << std::endl;
  u.setId(42);
  std::cout << "u.getId() = " << u.getId() << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/class9.cpp -o src/class9
$ ./src/class9
u.getId() = 7
u.getId() = 42
```

## Constructor

* Special method called when a new object of the class is created

* C++ provides a default constructor that takes no arguments

* You can replace the default constructor with a custom constructor by defining
a method name with the same name as the class

* Like other methods, the constructor can take arguments

* Does not return anything, not even void

### Constructor example

`src/class10.cpp`:

```c++
#include <iostream>

class user {
  int id;
 public://in most cases, you want the constructor to be public
  user(int id) { this->id = id; }
  int getId(void) { return id; }
};

int main() {
  user u(13);
  std::cout << "u.getId() = " << u.getId() << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/class10.cpp -o src/class10
$ ./src/class10
u.getId() = 13
```

### Constructor example
`src/class11.cpp`:

```c++
#include <iostream>

class user {
  int id;
 public:
  user(int id) { this->id = id; }
  int getId(void) { return id; }
};

int main() {
  user u; //calls default constructor, but doesn't exit -> need to declare on its own(for ex, set id=0 in constructor)
  std::cout << "u.getId() = " << u.getId() << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/class11.cpp -o src/class11
src/class11.cpp:11:8: error: no matching constructor for initialization of 'user'
  user u;
       ^
src/class11.cpp:6:3: note: candidate constructor not viable: requires single argument 'id', but no arguments were provided
  user(int id) { this->id = id; }
  ^
src/class11.cpp:3:7: note: candidate constructor (the implicit copy constructor) not viable: requires 1 argument, but 0 were provided
class user {
      ^
src/class11.cpp:3:7: note: candidate constructor (the implicit move constructor) not viable: requires 1 argument, but 0 were provided
1 error generated.
```

## Circle example

`src/circle1.cpp`:

```c++
#include <cmath>
#include <iostream>

class circle {
  double x, y, r;
 public:
  circle(double x, double y, double r) {
    this->x = x;
    this->y = y;
    this->r = r;
  }
  double getArea(void) {
    return M_PI*r*r;
  }
};

int main() {
  circle c(1.2, 3.4, 2.);
  std::cout << "c.getArea() = " << c.getArea() << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -Wall -Wextra -Wconversion src/circle1.cpp -o src/circle1
$ ./src/circle1
c.getArea() = 12.5664
```

## Multiple files

`src/circle2.hpp`:

```c++
//header gaurds
#ifndef CIRCLE2_HPP
#define CIRCLE2_HPP

class circle {
  double x, y, r;
 public:
  circle(double x, double y, double r);
  double getArea(void);
};

#endif /* CIRCLE2_HPP */
```

`src/circle2.cpp`:

```c++
#include <cmath>

#include "circle2.hpp" //header has class definition that we'll need later in the function

circle::circle(double x, double y, double r) { //member funcitons sit inside class namespace
  this->x = x;
  this->y = y;
  this->r = r;
}

double circle::getArea(void) { //member functions have different name
  return M_PI*r*r;
}
```

`src/main2.cpp`:

```c++
#include <iostream>

#include "circle2.hpp"

int main() {
  circle c(1.2, 3.4, 2.);
  std::cout << "c.getArea() = " << c.getArea() << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -Wall -Wextra -Wconversion src/circle2.cpp src/main2.cpp -o src/main2 -I./src
$ ./src/main2
c.getArea() = 12.5664
```

### Multiple files, example 2

`src/circle3.hpp`:

```c++
#ifndef CIRCLE3_HPP
#define CIRCLE3_HPP

namespace geometry { //namespace can access everything inside geometry (defining this namespace)

class circle {
  double x, y, r;
 public:
  circle(double x, double y, double r);
  double getArea(void);
  double getPerimeter(void);
};

}

#endif /* CIRCLE3_HPP */
```

`src/circle3a.cpp`:

```c++
#include <cmath>

#include "circle3.hpp"

namespace geometry {

circle::circle(double x, double y, double r) {
  this->x = x;
  this->y = y;
  this->r = r;
}

double circle::getArea(void) {
  return M_PI*r*r;
}

}

```

`src/circle3b.cpp`:

```c++
#include <cmath>

#include "circle3.hpp"

namespace geometry {

double circle::getPerimeter(void) {
  return 2.*M_PI*r;
}

}
```

`src/main3.cpp`:

```c++
#include <iostream>

#include "circle3.hpp"

int main() {
  geometry::circle c(1.2, 3.4, 1.8); // namespace is geometry
  std::cout << "c.getArea() = " << c.getArea() << std::endl;
  std::cout << "c.getPerimeter() = " << c.getPerimeter() << std::endl;
  return 0;
}
```

Output:

```
$ clang++ -Wall -Wextra -Wconversion src/circle3a.cpp src/circle3b.cpp src/main3.cpp -o src/main3 -I./src
$ ./src/main3
c.getArea() = 10.1788
c.getPerimeter() = 11.3097
```

## Objects and containers

`src/container.cpp`:

```c++
#include <iostream>
#include <vector>

#include "circle3.hpp"

int main() {
  std::vector<geometry::circle> circles; //vector is gonna contain circles
  circles.emplace_back(8.3, 4.7, 0.5); //push them at the end of vector (emplace does it inplace instead of push_back, which makes copies
  circles.emplace_back(4.1, 2.3, 1.4);
  circles.emplace_back(-3.2, 0.8, 14.4);

  for(auto& c : circles) {
    std::cout << "c.getArea() = " << c.getArea() << std::endl;
  }

  return 0;
}
```

Output:

```
$ clang++ -std=c++11 -Wall -Wextra -Wconversion src/circle3a.cpp src/circle3b.cpp src/container.cpp -o src/container -I./src
$ ./src/container
c.getArea() = 0.785398
c.getArea() = 6.15752
c.getArea() = 651.441
```

## Reading

**C++ Primer, Fifth Edition** by Lippman et al.

* Section 1.5: Introducing Classes
