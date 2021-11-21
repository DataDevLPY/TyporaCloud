# 1. Basic Python Syntax

## Sets

**set operations**

```python
A = {'a', 'b', 'c'}
B = {'a', 'd'}
A.union(B)		# A|B
A.intersection(B)		# A&B
A.difference(B)		# A-B
```

```python
AA = {'a', 'b'}
BB = {'c'}
AA.update(BB)
```

```python
A.add('d')
```

13687179736

## NumPy Array

```python
# To use this library, let's first import it
import numpy as np

# We can create a numpy 1-dim array
a = np.array([1,2,3]) 	# 1-dim array
print (type(a)) 	# check the type of the array
print (a.shape)
print (a[0], a[1], a[2])
print(a)


# Let's create a 2-dim array:
b = np.array([[1,2,3],[4,5,6]])
print(b.shape)
print(b[0,0])

# To create a 2x2 array of all zeros
c = np.zeros((2,2))
print(c)

```



​	Basic math functions can be used as follows:

```python
x = np.array([[1,2], [3,4]])
y = np.array([[5,6], [7,8]])
print (x+y) 	# Elementwise sum producing an array
print(np.add(x,y)) 	# What about this?
print (x-y) 	# Elementwise differences producing an array
print(np.subtract(x,y)) 	# What about this?
print (x*y) 	# Elementwise product producing an array
print(np.multiply(x,y)) 	# What about this?
print (x/y) 	# Elementwise sum producing an array
print(np.divide(x,y)) 	# What about this?
print(np.sqrt(x)) 	# Elementwise square root producing an array
```



​	A useful function that performs computations on arrays

```python
x = np.array([[1,2], [3,4]])
print(np.sum(x)) # sum of the all elements
print(np.sum(x, axis=0)) # sum of each column
print(np.sum(x, axis=1)) # sum of each row
```



## Useful built-in functions and defining lambda functions

**abs**

`abs` return the absolute value of a numer

```python
n = -21
print(abs(n))
```

**dict**

use `dict()` with a tuple or list as its argument

**enumerate**

We can use `enumerate()` to iterate an iterable object. It returns an enumerate object. More specifically, such an object can bee seen a list of tuples, each containing a pair of count/index and value. So this function is veru useful for using both index and value of each value in a list.

```python
menu = ['pizza', 'pasta', 'hamburger']
print(menu)
print(list(menu))
print(list(enumerate(menu)))
```



**sorted, sort**

```python
a = [3, 1, 7, 5, 9]
sorted(a)

# generate a descending sorted list
sorted(a, reverse=True)
```



**zip**

```python
A = [1, 2, 3]
B = ['a', 'b', 'c']
C = zip(A, B)
print (list(C))
```



**Lambda functions**

```python
A = [2, 18, 9, 22, 17]
print (list(filter(lambda x: x % 3 == 0, A)))
```



## Multi-processing functions

Multi-processsing is a core part of parallel programming. Given a job, in parallel programming, multiple processors are performed separately to complete the job. The job is split into the number of sub tasks and each processor is responsible for carrying out each sub task using a separate memory.

```python
# import the Python multiprocessing moddule
import multiprocessing as mp

# create a Pool object by defining the number of multiple parallel precessors that will perform together for parallel processing at the same time
pool = mp.Pool(processes = n_processor)

# call the pool.apply() method to perform funtionName in parallel
pool.apply(functionName, [argument_1, ..., argument_n])

```



​	Example:

```python
import multiprocessing as mp

def cube(x):
    return x**3

pool = mp.Pool(processes = 2)

results = [pool.apply(cube, [x]) for x in range (1,5)]
print(results)
```

```python
results = pool.map(cube, range(1,5))
```

```python
results = [pool.apply_async(cube, [x]) for x in range (1,5)]
output = [p.get() for p in results]
print(output)
```









