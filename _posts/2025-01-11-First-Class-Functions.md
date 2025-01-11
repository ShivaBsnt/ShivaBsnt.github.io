---
title: "First Class Functions in Python"
categories: "Python"
tags:
  - "Python"
  - "First-Class Functions"
  - "Functional Programming"
  - "Programming Concepts"
  - "Python Tips"
  - "Python Functions"
  - "Higher-Order Functions"

header:
  teaser: "/assets/images/2025-01-11-First-Class-Functions/teaser.webp"
---

![Cover Page](/assets/images/2025-01-11-First-Class-Functions/cover.webp)

# First Class Functions

"A programming language is said to have first-class functions if it treats functions as **first-class citizens**." - [wikipedia](https://en.wikipedia.org/wiki/First-class_function).

## What Does It Mean for Functions to Be First-Class?

It means that they can be treated like any other object, such as integers or strings. In other words, functions:

1. **Can be assigned to variables.**
2. **Can be passed as arguments to other functions.**
3. **Can be returned from other functions.**

Let's explore these aspects in detail.

## Assigning Functions to Variables

Functions in Python can be assigned to variables just like any other object:

```python
# Defining a function
def square(x):
    return x * x

# Assigning square function to variable.
f = square

print(f) # output: <function square at 0x7c5aaf0b00e0>
print(square) # output:<function square at 0x7c5aaf0b00e0>

# calling the function
print(f(5)) # output: 25

```

## Passing Functions as Arguments

Functions can be passed as arguments to other functions.

```python
# Defining a function
def square(x):
    return x * x

# Defining a function which takes two arguments
def func_map(func, arg_list):# func=>square, arg_list=>[1,2,3,4,5]
    result = []
    for arg in arg_list:
        result.append(func(arg)) # func(arg) => square(arg)
    return result

# Passing a square function and list as an argument
squares = func_map(square, [1, 2, 3, 4, 5])

print(squares) # output: [1, 4, 9, 16, 25]
```

## Returning Functions from Other Functions

Functions can also return other functions.

```python
# Defining a function that returns another function
def html_tag(tag):
    # Defining a wrap_text function inside html_tag function
    def wrap_text(msg):
        print(f"<{tag}>{msg}</{tag}>")
    return wrap_text # returning a function wrap_text

# passing argument 'h1' to html_tag function
f = html_tag('h1')

print(f)
# output: <function html_tag.<locals>.wrap_text at 0x7d511d884040>

f("Hi! my name is Shiva.") # output:<h1>Hi! my name is Shiva.</h1>
f("I live in Nepal.") # output:<h1>I live in Nepal.</h1>

```

## Use Cases for First-Class Functions

1. **Functional Programming**: Functions like `map()`, `filter()`, and `reduce()` leverage first-class functions to enable concise functional-style operations.
2. **Callbacks**: Pass functions as arguments for event-driven programming.
3. **Dynamic Behavior**: Generate functions dynamically or return specialized functions based on input conditions.

## Conclusion

The concept of first-class functions is fundamental in Python, enabling powerful and flexible code structures. By understanding and leveraging this concept, you can write more efficient and elegant Python programs.

Do you use first-class functions in your projects🤔? Share your experiences in the comments below🗈✍️👇!
