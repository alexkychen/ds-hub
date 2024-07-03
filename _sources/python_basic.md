---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Python Basics

## Numbers and arithmetic
```{code-cell} ipython3
a = 2 # int 
b = 6.0 # float
c = 12 + 0j # complex 

print(f"a + b = {a + b}") # addition
print(f"b - a = {b - a }") # substraction
print(f"a * b = {a * b}") # multiplication
print(f"b / a = {b / a}") # division
print(f"b ** a = {b ** a}") # exponentiation 
print(f"5 // 2 = {5 // 2}") # get quotient 
print(f"5 % 2 = {5 % 2}") # get remainder
```

## String 
String variables are either single- or double quoted. `a = "hello"` or `a = 'hello'`
```{code-cell} ipython3
a = "hello"
b = "world"

# string can be added 
a + b # = "helloworld"

# print variable 
print(a + " " + b) # > hello world
```
## Arrays
```{list-table} Python array data type and property
:header-rows: 1

* - property
  - Set 
  - List
  - Tuple
  - Dictionary
* - indexable
  - No
  - Yes
  - Yes
  - Yes (use key)
* - addable/removable
  - Yes
  - Yes
  - No
  - Yes
* - repeatable
  - No
  - Yes
  - Yes
  - Yes for value, No for key
* - sortable
  - No
  - Yes
  - No
  - Yes

```

### Set 
Set is a group of unique elements. No duplicates; non-ordered; non-indexable
```{code-cell} ipython3
# create empty set 
example_set = set()

# create a set with elements
odd = {3, 1, 7, 4} 
even = {4, 2, 6}

print(f"A set of odd numbers: {odd}")
print(f"A set of even numbers: {even}")
print("Note that the order of elements differs from creation.")
```

Remove an element 
```{code-cell} ipython3
odd.remove(4)
odd
```

Use `remove` to remove an element not eixst will give traceback. One can use `discard` to remove an element to skip traceback if the element does not exist 
```{code-cell} ipython
odd.discard(10)
```

Add an element
```{code-cell} ipython3
even.add(8)
even
```

Union between sets
```{code-cell} ipython3
num_set = odd.union(even)
num_set
```

Find intersection between sets 
```{code-cell} ipython3
set1 = {0, 1, 'apple', 'orange'}
set2 = {True, 'orange', 'lemon'}
set3 = set1.intersection(set2)
set3
```

Clear elements in a set
```{code-cell} ipython3
set3.clear()
set3
```

### List 

```{code-cell} ipython3
odd = [3, 1 , 7, 4]
even = [4, 2, 6]

print(f"A list of odd numbers: {odd}")
print(f"A list of even numbers: {even}")
```
Access element in a list using index
```{code-cell} ipython3
a = odd[0]
b = even[-1]
c = odd[:2]
print(f"The first element of odd list: {a}")
print(f"The last elment of even list: {b}")
print(f"The first two elements of odd list: {c}")
```

Add elements to list: use index, append, insert, extend, or + 
```{code-cell} ipython3
# use index
odd[-1] = 9

# use append
even.append(8)

# use insert (element, index)
odd.insert(5, 2)

# use extend 
even.extend([10, 12])

print(f"odd: {odd}")
print(f"even: {even}")
```

List addition
```{code-cell} ipython3
odd + even
```

Remove elements in a list: pop, remove, clear
```{code-cell} ipython3
# remove the last element 
even.pop()
print(even)

# remove element via index
even.pop(0) # first element
print(even)

# remove 10 
even.remove(10)
print(even)

# remove all elements
example_list = [1,2,3]
example_list.clear()
print(example_list)
```

Other list operations: count, index, copy, reverse, sort
```{code-cell} ipython3
# count how many element 2 in even
cnt = even.count(2)
print(f"Number of 2 in even: {cnt}")

# get the index of element 2 
idx = even.index(2)
print(f"Index of 2 in even: {idx}")

# create a copy of odd
odd_copy = odd.copy()
print(odd_copy)

# reverse the element order in odd_copy
odd_copy.reverse()
print(odd_copy)

# sort element in odd_copy
odd_copy.sort()
print(odd_copy)
```

### Tuple
Elements can not be addred or removed from the tuple. 
```{code-cell} ipython3
# create a tuple 
even = (2, 4, 2)

# access element using index 
a = even[0] 
print(f"first element: {a}")

# get index of a value 
idx = even.index(2) # return the index of value 2; return the first index if duplicates 
print(f"index of 2: {idx}")

# count the numebr of a value 
cnt = even.count(2)
print(f"number of 2: {cnt}")
```

### Dictionary
Dictionary comprises a list of key:value pairs. Keys are unique in the dictionary.

```{code-cell} ipython3
# fromkeys, get, items, keys, pop, popitem, update, values 
# create a dictionary
example = {"A": 0, "B": 1, "C": 2} 
example2 = dict(A = 0, B = 1, C =3)
print(example)

# add key:value
example["D"] = 4
print(example)

# remove key:value 
example.pop("A")
print(example)
#remove the last key-item pair
example.popitem() 
print(example)

# modify key:value
example["C"] = 3
example.update({"C": 3})
print(example)
```

Access value by key
```{code-cell} ipython3
B = example["B"]
print(f"Value of key 'B': {B}")

# return None if key 'b' not found
u = example.get("b", None)
print(f"Value of key 'b': {u}")
```

Loop through dictionary
```{code-cell} ipython3
print("Get keys")
for key in example.keys():
  print(key)

print("Get key:value pairs")
for key, val in example.items():
  print(f"{key}:{val}")
```

## Function
Write a function to check if a number is odd or even 
```{code-cell} ipython3
def check_number(n):
  """Function to check a number"""
  if type(n) != int:
    raise TypeError("Argument should be an integer")
  
  if n == 0:
    return "zero"
  elif n % 2 == 0:
    return "even and greater than zero"
  else:
    return "odd"

a33 = check_number(33)
a22 = check_number(22)
a0 = check_number(0)

print(f"33 is {a33}")
print(f"22 is {a22}")
print(f"0 is {a0}")
```

Test for error
```{code-block} python
a = check_number('a')
```
```{code-block} python
TypeError: Argument should be an integer
```

## Class and object
Object Oriented Programming (OOP): Create objects(or instances) and methods using Python classes.
```{code-cell} ipython3
class User:
    """User class docstring"""
    def __init__(self, name, birthday) -> None:
        self.full_name = name 
        self.birthday = birthday 
        
        # extract first and last name
        user_name = self.full_name.split(" ")
        self.first_name = user_name[0]
        self.last_name = user_name[1]

    def age(self):
        """Return user age"""
        age_year = self.birthday[0:4]
        self.age = 2023 - int(age_year)
        return self.age

user1 = User("Joe Smith", "19800101")

print(user1.first_name)
print(user1.last_name)
print(user1.age())
```
**Name convention**: In the above example, `user1` is an **object** or **instance**. The `first_name` or `last_name` of object `user1` is **field**.

```{code-cell} ipython3
# check docstring text 
help(User)
```
**Class special methods**
- `__doc__` : call docstring 
- `__init__`: initialize a class with variables
- `__dict__`: call class attributes and values 
- `__str__`: define `str()` function for the class

```{code-cell} ipython3
class Citizen:
  """This is description for class Citizen"""
  
  def __init__(self, fn, ln):
    """Initialize Citizen class with first and last name"""
    self.first_name = fn
    self.last_name = ln
  
  def __str__(self):
    return f"{self.first_name.capitalize()} {self.last_name.capitalize()}"
  

c1 = Citizen("joe", "smith")
c2 = Citizen("mary", "Ann")
# assign a post-defined attribute
c1.brithday = "1990-2-25"

# check doc string
print(c1.__doc__)

# call attribute and value
print(c1.__dict__)
print(c2.__dict__)

# str method
print(str(c2))
```

## Exceptions
Use `try`, `except`, `else` and `finally` to manage errors of program workflow. Using try and except blocks can help program continue to run while catching what errors might occur in the try block.
```{code-block} python
try:
    # run first
    pass

except NameError:
    # run if NameError occurs in try block
    pass

except Exception as e:
    # (optional) run if error other than NameError occurs in try block 
    # print the error message
    print(e)

else:
    # run here when try block works
    # run here when *none* of except blocks are executed 
    pass 

finally:
    # run here *always*
    pass
```

One can also use `raise` to raise an error in the code.
```{code-block} python 
x = 10
if x > 5:
  raise Exception("x should not exceed 5")
```
```{code-block} python
Exception: x should not exceed 5
```
More on Exception Handling

<iframe width="560" height="315" src="https://www.youtube.com/embed/NMTEjQ8-AJM?si=6wuBNp1ZEME_x92U" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## List comprehension
Use for loop in a list to create a new list
```{code-cell} ipython3
# squared numbers from 0 to 10
lc1 = [i**2 for i in range(0,11)]
print(lc1)

# squared numbers of even numbers from 0 to 10 
lc2 = [i**2 for i in range(0,11) if i % 2 == 0]
print(lc2)

# cartesian product 
A = [1,3,5]
B = [2,4,6]
lc3 = [(a, b) for a in A for b in B]
print(lc3)

```

## Lambda expression
Write an anonymous function in a single line of code. Multiple variables are allowed.
```{code-cell} ipython3
f = lambda x: (x + 1) * 2
print(f(2))

# process a name
full_name = lambda fn, ln: fn.strip().title() + " " + ln.strip().title()
print(full_name(" Joe", "SMITH "))
# 'Joe Smith'
```

## Iterator
Python iterables inlcude list, tuple, string and byte objects.
```{code-block} python
# loop through a list  
for element in ["A","L","E","X"]: 
  print(element)

# loop through a tuple 
for element in ("a","l","e","x"):
  print(element)

# loop through a string 
for character in "ALEX":
  print(character)

# loop through a b
for byte in b'ALEX':
  print(byte)
```

Build an iterator using `iter` and `next` functions. **It can help loop over things that have unknown lengths**.
```{code-cell} ipython3
fruits = ("apple","orange", "lemon")

# turn an array to iterator
looper = iter(fruits)

print(next(looper))
print(next(looper))
print(next(looper))
```
When the looper is exhausted, running `next()` will result in traceback
```{code-block} python
next(looper)
```
```{code-block} python
StopIteration    Traceback (most recent call last)
```

Create a class with built-in iteration
```{code-cell} ipython3
class Portfolio:
  def __init__(self):
    self.holdings = {} # key:ticker, value:shares

  def buy(self, ticker, shares):
    self.holdings[ticker] = self.holdings.get(ticker, 0) + shares 

  def sell(self, ticker, shares):
    self.holdings[ticker] = self.holdings.get(ticker, 0) - shares

  def __iter__(self):
    return iter(self.holdings.items())

p = Portfolio()
p.buy("APPL", 10)
p.buy("TSLA", 20)
p.sell("APPL", 5)
p.buy("TSLA", 5)

for ticker, share in p:
    print(ticker, "-", str(share))
```

More on using `itertool` module

<iframe width="560" height="315" src="https://www.youtube.com/embed/baZpqVmR488?si=3e5NiFasxiR4mz0i" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Generator
Generator is one type of iterators. A function uses `yield` instead of `return` is called generator. It can help save time and memory when running a loop through a large iterable.
```{code-cell} ipython3
import random

# Return a list of 100 random integer (0-99)
def number_list():
    num_list = []
    for i in range(100):
        num = random.randint(0,100)
        num_list.append(num)
    return num_list 

# Generate a sequence of 100 random intger (0-99)
def number_generator():
    for i in range(100):
        num = random.randint(0, 100)
    yield num 
```
```{code-cell} ipython3
next(number_generator())
```
```{code-cell} ipython3
next(number_generator())
```

## Map, filter and reduce
Functional programming: Apply a function to iterable data (e.g., list, tupe) in one line of code, instead of writing loops.

### Map
- `map(func, *iterables)`

Apply a function to one iterable or multiple iterables when the passing function using multiple arguments. The passing function will return the result of each element in the iterable.

```{code-cell} ipython3
# example of one iterable
names = ["alice", "ben","charlie"]
map_object = map(lambda x: x.capitalize(), names)
print(map_object)
print(list(map_object))
``` 

```{code-cell} ipython3
floats = [1.235, 2.336, 3.4562]
decimals = [1, 2, 3]
map_object = map(round, floats, decimals)
print(list(map_object))
```

### Filter
- `filter(func, iterable)`

Apply a function that returns boolean values (True or False) to the iterable (one iterable only). Then it returns only the elements that is evaluated to True.

```{code-cell} ipython3
nums = [35, 48 , 50, 66, 87, 95]

def greater_than_50(x):
  if x > 50:
    return True

filter_object = filter(greater_than_50, nums)
print(next(filter_object))
print(next(filter_object))
print(next(filter_object))
print(list(filter_object))
```

### Reduce
- `reduce(func, iterable [, initial])`

Apply a function with 2 arguments that takes first and next element in the iterable in a cumulative way. If the third argument (i.e., initial) is supplied, then it will become the first element. In Python 3, the `reduce` needs to be imported from `functools` library 

```{code-cell} ipython3
from functools import reduce 

nums = [1, 2, 3, 4]

def custom_sum(x, y):
  return x + y

# without initial supplied
res1 = reduce(custom_sum, nums)
print(res1)

# with initial supplied
res2 = reduce(custom_sum, nums, 10)
print(res2)
```
 
## Sorting

- `mylist.sort()`: Sort elements in a list and replace the original list.
- `nlist = sorted(mylist)`: Sort elements in a list or other iterables and return a list.
- Both `list.sort()` and `sorted()` have a `key` argument to indicate the way of sorting. E.g., Specify which element in arrays of arrays is used for sorting. See example below.

Sort a string list
```{code-cell} ipython3
# sort a list 
mylist = ['b', 'a', 'A', 'C', 'c']
mylist.sort()
print(mylist)

# sort with reversed order
mylist.sort(reverse=True)
print(mylist)
```
Sort a list of tuples by the 2nd element in the tuple
```{code-cell} ipython3
tuple_list = [("B", 20) ,("A", 30), ("C", 10)]
tuple_list.sort()
print(tuple_list)

# sort by 2nd element (number) in the tuples
num = lambda tup: tup[1]
tuple_list.sort(key=num)
print(tuple_list)
```

Sort elements in a tuple. 
```{code-cell} ipython3
mytuple = (5,3,8,1)
sorted_mytuple = sorted(mytuple)
print(sorted_mytuple)
```
**Note**: `mytuple.sort()` does not work because tuple object is immutable.
