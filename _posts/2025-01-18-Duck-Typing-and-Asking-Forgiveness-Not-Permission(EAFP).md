---
title: "Duck Typing and Asking Forgiveness, Not Permission (EAFP)"
categories: "Python"
tags:
  - "Python Programming"
  - "Duck Typing"
  - "EAFP (Easier to Ask for Forgiveness than Permission)"
  - "Programming Concepts"
  - "LBYL vs EAFP"
  - "Python"

header:
  teaser: "/assets/images/2025-01-18-Duck-Typing-and-Asking-Forgiveness-Not-Permission(EAFP)/teaser.webp"
---

![Cover Page](</assets/images/2025-01-18-Duck-Typing-and-Asking-Forgiveness-Not-Permission(EAFP)/cover.webp>)

# Duck Typing

"A programming style which does not look at an object’s type to determine if it has the right interface; instead, the method or attribute is simply called or used (**“If it looks like a duck and quacks like a duck, it must be a duck.🦆”**)" - [Python Glossary](https://docs.python.org/3/glossary.html#term-duck-typing)

In Python, this means that if an object behaves like a particular type (i.e., it has all the methods and properties expected of that type), then it can be used as that type, regardless of its actual class.

**Example:**

```python
class Duck:
    def quack(self):
        print('Quack, Quack!')

    def fly(self):
        print('Flap, Flap!')

class Person:
    def quack(self):
        print("I'm quacking like a duck—quack, quack!")

    def fly(self):
        print("I'm flapping my arms—flap, flap!")

def quack_and_fly(obj):
    obj.quack()
    obj.fly()

d = Duck()
p = Person()
quack_and_fly(d)
# output
# Quack, Quack!
# Flap, Flap!

quack_and_fly(p)
# output
# I'm quacking like a duck—quack, quack!
# I'm flapping my arms—flap, flap!
```

In this example, the **quack_and_fly** function doesn't care whether the **obj** parameter is an instance of **Duck**, **Person**, or any other class. It only cares that the obj has a **quack** and **fly** method that can be called.

## Embracing Duck Typing In Python

Python itself is designed with duck typing in mind, and many of its built-in functions and libraries embrace this principle. Here are some built-in examples in Python that illustrate duck typing:

### 1. The len() Function

The '**len()**' function doesn't check the type of its argument. Instead, it simply looks for an object that implements the **\_\_len\_\_()** method.

```python
class CustomCollection:
    def __len__(self):
        return 21

obj = CustomCollection()
print(len(obj))  # Output: 21
```

## 2. Iteration with for Loops

Python's '**for**' loop relies on an object implementing the iterator protocol (**\_\_iter\_\_()** and **\_\_next\_\_()** methods). It doesn't check if an object is a specific type, like a list or tuple.

```python
class CustomIterable:
    def __iter__(self):
        return iter([3, 6, 9])

obj = CustomIterable()
for item in obj:
    print(item)
# Output:
# 3
# 6
# 9
```

### 3. The in Keyword

The '**in**' keyword uses the **\_\_contains\_\_()** method, but it can also fall back to iterating over the object. It doesn't require the object to be of a specific type.

```python
class CustomContainer:
    def __contains__(self, item):
        return item == "duck"

obj = CustomContainer()
print("duck" in obj)  # Output: True
print("cat" in obj)   # Output: False

```

These examples show how Python's built-in mechanisms are inherently duck-typed. Instead of checking explicit types, Python relies on the presence of specific methods or attributes, making the language highly flexible and dynamic.

# Easier To Ask For Forgiveness Than Permission (EAFP)

"Easier to ask for forgiveness than permission. This common Python coding style assumes the existence of valid keys or attributes and catches exceptions if the assumption proves false. This clean and fast style is characterized by the presence of many **try** and **except** statements. The technique contrasts with the **LBYL**(Look Before You Leap) style common to many other languages such as C." - [Python Glossary](https://docs.python.org/3/glossary.html#term-EAFP)

In Python, this means that instead of checking if something is possible or safe to do, just go ahead and try it. If it fails, handle the error.

## Example:

```python
# EAFP way
try:
    result = 10 / 0  # Attempt risky operation
except ZeroDivisionError:
    result = "Cannot divide by zero!"

print(result)  # Output: Cannot divide by zero!

```

## EAFP vs Checking First: Without EAFP:

```python
# LBYL (Look Before You Leap)
denominator = 0
if denominator != 0:
    result = numerator / denominator
else:
    result = "Cannot divide by zero!"
print(result) # output: Cannot divide by zero!
```

# How Duck Typing and EAFP Work Together

These two principles often complement each other. For instance:
You can assume an object has a specific method (Duck Typing) and use a try-except block (EAFP) to handle cases where the assumption fails.

```python
class Duck:
    def quack(self):
        print('Quack, Quack!')

    def fly(self):
        print('Flap, Flap!')

# Define the Person class
class Person:
    def quack(self):
        print("I'm quacking like a duck—quack, quack!")

    def fly(self):
        print("I'm flapping my arms—flap, flap!")

# Define a function to perform actions
def quack_and_fly(obj):
    """
    This function demonstrates Duck Typing and EAFP.
    It assumes the object 'obj' has 'quack' and 'fly' methods.
    If the object is missing a required method (e.g., 'bark'),
    it handles the exception gracefully.
    """
    try:
        obj.quack()
        obj.fly()
        # Intentionally call a method that doesn't exist
        obj.bark()
    except AttributeError:
        # Handle the missing method
        print("Cannot perform this action!")

# Create an instance of the Duck class
d = Duck()
# Create an instance of the Person class
p = Person()

quack_and_fly(d)
# Output:
# Quack, Quack!
# Flap, Flap!

quack_and_fly(p)
# Output:
# I'm quacking like a duck—quack, quack!
# I'm flapping my arms—flap, flap!
# Cannot perform this action!

```

# Conclusion

**Duck Typing** and **EAFP** are at the heart of Python's dynamic and flexible nature.
Together, these principles enable Python developers to write clean, efficient, and **Pythonic** code that is easy to maintain and extend. By embracing these patterns, programmers can handle diverse objects and unexpected errors gracefully, leading to robust applications.
