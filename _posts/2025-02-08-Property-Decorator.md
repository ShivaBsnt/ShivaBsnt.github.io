---
title: "Property Decorators - Getters, Setters, and Deleters"
categories: "Python"
tags:
  - "@property"
  - "Property Decorator"
  - "Python OOP"
  - "Object-Oriented Programming"
  - "Python Decorators"
  - "Encapsulation"
  - "Python"

header:
  teaser: "/assets/images/2025-02-08-Property-Decorator/teaser.webp"
---

![Cover Page](/assets/images/2025-02-08-Property-Decorator/cover.webp)

Python is celebrated for its simplicity and readability, and one of the features that contribute to its elegance is the **@property** decorator. This powerful property function enables developers to manage attribute access with ease, offering both simplicity in code usage and control behind the scenes.

## The Motivation Behind @property

In object-oriented programming, encapsulation is key. You might have experienced scenarios where you need to:

- **Protect data integrity**: Ensure that class attributes are not arbitrarily changed.
- **Compute values on the fly:** Provide attributes that are computed dynamically while maintaining a simple attribute-like access.
- **Maintain backward compatibility:** Transition from public attributes to properties without changing the interface.

Before **@property**, one common approach was to use explicit getter and setter methods. For example:

```python
class Person:
    def __init__(self, name):
        self._name = name  # Notice the convention of using an underscore

    def get_name(self):
        return self._name

    def set_name(self, value):
        if not isinstance(value, str):
            raise ValueError("Name must be a string.")
        self._name = value
```

While this works, it forces the caller to use methods **person.get_name()**, which is less natural than accessing an attribute **person.name**. The **@property** decorator bridges this gap elegantly.

## Understanding the Basics of @property

The @property decorator turns a method into a “getter” for a read-only attribute. Here’s a simple example:

```python
class Person:
    def __init__(self, name):
        self._name = name  # Use a “private” attribute by convention

    @property
    def name(self):
        """The name property."""
        return self._name

# Usage:
p = Person("Alice")
print(p.name)  # Output: Alice
```

Notice that even though **name** is defined as a method, accessing **p.name** looks like accessing a simple attribute. It also allows you to add logic when needed without changing how the attribute is accessed.

## Adding a Setter: Controlled Write Access

If you want the attribute to be mutable while still controlling how its value is set (e.g., adding validation), you can define a setter using the **`@<property_name>.setter`** decorator:

```python
class Person:
    def __init__(self, name):
        self._name = name

    @property
    def name(self):
        """The name property."""
        return self._name

    @name.setter
    def name(self, value):
        if not isinstance(value, str):
            raise ValueError("Name must be a string.")
        self._name = value

# Usage:
p = Person("Alice")
p.name = "Bob"  # Calls the setter to update the value
print(p.name)   # Output: Bob
```

With this approach, you can add error-checking, logging, or any other processing logic when setting an attribute.

## The Complete Property: Getter, Setter, and Deleter

For full control, Python’s property can also include a deleter that defines behavior when an attribute is deleted. Here’s an example:

```python
class Person:
    def __init__(self, name):
        self._name = name

    @property
    def name(self):
        """The name property."""
        return self._name

    @name.setter
    def name(self, value):
        if not isinstance(value, str):
            raise ValueError("Name must be a string.")
        self._name = value

    @name.deleter
    def name(self):
        print("Deleting name...")
        del self._name

# Usage:
p = Person("Alice")
del p.name  # Output: Deleting name...
```

## Under the Hood: The property Built-in

While the decorator syntax is the most common way to use properties, it’s useful to know that @property is essentially a shorthand for the built-in property() function. The general form is:

```python
property(fget=None, fset=None, fdel=None, doc=None)
```

```python
class Person:
    def __init__(self, name):
        self._name = name

    def get_name(self):
        return self._name

    def set_name(self, value):
        self._name = value

    def del_name(self):
        del self._name

    name = property(get_name, set_name, del_name, "The name property.")

```

## Conclusion

By allowing methods to be accessed like attributes, properties offer a clean, intuitive, and powerful way to manage object state, enforce data integrity, and encapsulate implementation details. Whether you’re refactoring legacy code or designing a new system, understanding and leveraging @property can lead to more maintainable and robust Python code.
