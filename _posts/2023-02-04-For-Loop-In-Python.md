---
title: Behind The Scene (For Loop in Python)
categories: DataStructure Algorithm
tags:
  - Python

header:
  teaser: "/assets/images/2023-02-04-For-Loop-In-Python/behind_the_scene_for_loop.png"
---

![Cover Page](/assets/images/2023-02-04-For-Loop-In-Python/behind_the_scene_for_loop.png)

## The for statement
The for statement in **Python** is used to iterate over the elements of a sequence (such as a string, tuple or list) or other iterable object. [(https://docs.python.org/)](https://docs.python.org/3/reference/compound_stmts.html#for)

{% highlight python linenos %}
programming_language = ['C', 'C#', 'Java', 'Python']

for language in programming_language:
    print(language)

{% endhighlight %}

##### Output
    C
    C#
    Java
    Python

We've constructed a list named programming_language in the example above. The value of language is at first set to first element list i.e **"C"** and then the print statement is executed. After that, language is updated with next element **"C#"**  and print statement is executed again. This way the loop runs until and unless the last element of list is accessed.

### Behind The Scene
From example shown above, we can now use **for** statement to iterate over each elements in sequence one by one. But do we know how it works? What's underneath? How it is able to access the element one by one?

Let me explain it. Behind the scenes, the for statement calls **iter()** on the container object (**programming_language**) which returns an iter object. (***<list_iterator object at 0x7f6099e53460>***)
{% highlight python linenos %}
programming_language = ['C', 'C#', 'Java', 'Python']
z = iter(programming_language) 
print(z)
# output - > <list_iterator object at 0x7f6099e53460>
{% endhighlight %}

The iter object has **"__next__()"** method which access elements in the container one at a time. 
{% highlight python linenos %}
programming_language = ['C', 'C#', 'Java', 'Python']
z = iter(programming_language) 
print(z)
# output - > <list_iterator object at 0x7f6099e53460>

print(z.__next__()) # output -> C
print(z.__next__()) # output -> C#
print(z.__next__()) # output -> Java
print(z.__next__()) # output -> Python

{% endhighlight %}

When there are no more elements, **"__next__()"** raises a **StopIteration** exception which tells the for loop to terminate.

{% highlight python linenos %}
programming_language = ['C', 'C#', 'Java', 'Python']
z = iter(programming_language) 
print(z)
# output - > <list_iterator object at 0x7f6099e53460>

print(z.__next__()) # output -> C
print(z.__next__()) # output -> C#
print(z.__next__()) # output -> Java
print(z.__next__()) # output -> Python

print(z.__next__()) # output -> StopIteration Exception

{% endhighlight %}

We can also call the **"__next__()"** method using the  built-in function **next()**.

{% highlight python linenos %}
programming_language = ['C', 'C#', 'Java', 'Python']
z = iter(programming_language) 
print(z)
# output - > <list_iterator object at 0x7f6099e53460>

print(next(z)) # output -> C
print(next(z)) # output -> C#
print(next(z)) # output -> Java
print(next(z)) # output -> Python

print(next(z)) # output -> StopIteration Exception
{% endhighlight %}

<div align="center"> 🅷🅰🅿🅿🆈 🅲🅾🅳🅸🅽🅶 </div>
