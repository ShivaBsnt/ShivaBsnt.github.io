---
title: Hash Table
categories: DataStructure Algorithm
tags:
  - Python
  - Data Structure
  - Algorithm
  - Dictionary

header:
  teaser: "/assets/images/2022-12-10-Hash-Table/hash.png"
---

![Cover Page](/assets/images/2022-12-10-Hash-Table/hash.png)
### Hash Table
It is a data structure where data is stored in key-value pairs. It uses a hash function to map data of arbitrary size to a fixed size and an array to store the data. The hash table has the time complexity of O(1) due to which it is extremely fast. Since most of the programming language has an inbuilt hash table, we do not have to necessarily create our own. Dictionary in python is an example of a hash table and it uses SipHash.

![grocery](/assets/images/2022-12-10-Hash-Table/grocery.jpg)
Let’s suppose that we work in the grocery shop. When a customer purchases a product from the shop, we have to go through the price in the book. And what if the price of the book is unalphabetized? We have to look at every single line to search for an apple. Do you know how much time that will take? O(n) times. And if the book is in alphabetical order, we can go for binary search which will take O(logn) times. If you want to learn more about linear and binary search and its time complexity, please refer to this link. (Binary Search vs Linear Search)


![apple cost](/assets/images/2022-12-10-Hash-Table/apple_cost.png)
 But looking at a book every time a customer asks for a product is painful. What if, you had a friend Harry who has memorized all the products and its cost? Every time, a customer asks for something, you can ask Harry and he will tell you the price immediately. Hash table exactly works like that, give it a key which is “apple” and it returns the price 33 cents.

### Example of Hash Table in python

{% highlight python linenos %}
# Creating hash table
grocery = {}

# grocery is a new hash table, 
# let's add prices for fruits
grocery = {}
grocery["apple"] = 33 # cost of apple
grocery["banana"] = 23 # cost of banana
grocery["cherry"] = 66 # cost of cherry
grocery["orange"] = 48 # cost of orange

# prices of all fruits
print(grocery)
# output
# {'apple': 33, 'banana': 23,
# 'cherry': 66, 'orange': 48}

# price of an apple
print(grocery["apple"])
# output
# 33
{% endhighlight %}