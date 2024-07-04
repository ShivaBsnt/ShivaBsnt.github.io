---
title: "Interning in Python: How It Saves Memory 🐍💡"
categories: "Python"
tags:
  - "Python"
  - "Memory Optimization"
  - "Interning"
header:
  teaser: "/assets/images/2023-07-15-Build-a-Custom-Connector-DOMO/domo-cover.png"
---

![Cover Page](/assets/images/2023-07-15-Build-a-Custom-Connector-DOMO/domo-cover.png)


When working with Python, efficiency is often a key consideration, particularly when dealing with large-scale applications or data. One of the techniques Python employs to enhance performance and save memory is called interning. This blog post will delve into what interning is, how it works in Python, and the benefits it offers in terms of memory management.

## What is Interning? 🧠
Interning is an optimization technique used to store only one copy of immutable objects that have the same value. In simpler terms, if you have multiple identical immutable objects (like strings or integers) in your program, Python will store just one copy of that object and reuse it wherever needed.

## Automatic Interning 🤖
Python automatically interns certain strings and integers to optimize memory usage and performance. For example, strings that resemble identifiers (e.g., variable names) and small integers (typically in the range of -5 to 256) are interned automatically.

### String Interning  ‎‧₊˚✧[text]✧˚₊‧
Let's see an example with strings. Python automatically interns short strings (typically those that look like identifiers and are under 20 characters).
```python 
# Creating two variables with the same short string value
a = "hello"
b = "hello"

# Checking if both variables point to the same object in memory
print(a is b)  # True
print(id(a))   # Example output: 4376642864
print(id(b))   # Example output: 4376642864
```
In this example:
- **_a and b are assigned the string "hello". When Python encounters string literals like "hello", it automatically interns them if they are short strings, typically for efficiency reasons. Therefore, both a and b point to the same memory location, which is why a is b returns True._**
- **_The id() function returns the memory address of the object. Since a and b refer to the same interned string object, id(a) and id(b) return the same memory address, which in the example could be 4376642864._**

### Integer Interning  🔢
Now, let's look at an example with small integers. Python automatically interns integers in the range **-5 to 256**.
```python 
# Creating two variables with the same small integer value
a = 100
b = 100

# Checking if both variables point to the same object in memory
print(a is b)  # True, because 100 is interned

print(id(a))   # Example output: 4372396304
print(id(b))   # Example output: 4372396304
```

In this example:

- **_a and b are assigned the integer 100. Because 100 is within the range of small integers that Python interns, both a and b refer to the same memory location. Hence, a is b returns True._**
- **_The id() function returns the memory address of the object. Since a and b refer to the same object, id(a) and id(b) return the same memory address, which in the example could be 4372396304._**


## Manual Interning 🧑‍🔧
For strings that are not automatically interned, you can use the **sys.intern()** function to intern them manually. This is particularly useful when you have a large number of string comparisons, as it can significantly improve performance.
```python 
a = "this is a long string with special characters!@#$%^&*()"
b = "this is a long string with special characters!@#$%^&*()"

# Check if they are the same object in memory
print(a is b)  # Output: False
```
Even though the content of a and b is identical, they are not the same object in memory because they are not automatically interned.

To intern such strings manually, you can do the following:
```python 
import sys

a = sys.intern("this is a long string with special characters!@#$%^&*()")
b = sys.intern("this is a long string with special characters!@#$%^&*()")

# Check if they are the same object in memory
print(a is b)  # Output: True
```

# Benefits of Interning 🌟
1. **Memory Efficiency**: By storing only one copy of identical immutable objects, interning reduces the overall memory footprint of your program. This is particularly useful in programs that handle large amounts of data with many repetitive elements.

2. **Performance Improvement**: Interning can lead to faster comparisons. Since interned objects share the same memory location,identity checks (is) become quicker as they involve comparing memory addresses rather than actual values.


# Conclusion 🎉
Interning is a powerful optimization technique in Python that can lead to significant memory savings and performance gains. By understanding how Python handles interning and knowing when to leverage it, you can write more efficient and optimized code.
