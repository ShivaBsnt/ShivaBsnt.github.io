---
title: Linear Search Vs Binary Search
categories: DataStructure Algorithm
tags:
  - Python
  - Data Structure
  - Algorithm
  - Linear Search
  - Binary Search

header:
  teaser: "/assets/images/2022-12-03-Binary-Search-Vs-Linear-Search/searchalgorithm.png"
---

![Cover Page](/assets/images/2022-12-03-Binary-Search-Vs-Linear-Search/searchalgorithm.png)

### Linear Search

Linear Search is defined as a sequential search algorithm that starts at one end and goes through each element of a list until the desired element is found, otherwise the search continues till the end of the data set. It is the easiest searching algorithm. [(https://www.geeksforgeeks.org/linear-search/)](https://www.geeksforgeeks.org/linear-search/)

Suppose that we want to call our friend **John** for a birthday party but before that, we have to find a phone number from a phone book. Now we started to check whether the name **John** exists or not from the top of our phone book. Is the first name in the phone book **John**? Probably not (it's probably a name that begins with 'A'). Ok, let's keep searching. Is the next name **John**? Probably not. Keep looking at the name until we find **John**.

What if the phone book had **1000** names? In the worst case, you have to read through all **1000** names, a damn hard job right? This is the sequential approach of search and we have to go through each item one by one until and unless we find the item we are looking for.

### Linear search in python
{% highlight python linenos %}
def linear_search(my_list, search_item):
    for index, item in enumerate(my_list):
        if item == search_item:
            return "found", index
    return "not found", -1

linear_search([1,2,3,4,5,6,7,8], 22)
{% endhighlight %}

### Binary Search
Binary Search is a searching algorithm for finding an element's position in a sorted array. It is comparatively faster than linear search.
[(https://www.programiz.com/dsa/binary-search)](https://www.programiz.com/dsa/binary-search)

Let's say that we got lucky in this case and the phone book is ordered in ascending order. Now the phone book starting page contains a name starting with 'A'. 'Adam', 'Arun', 'Ava', and so on, and the last page contains a name with 'z' like 'Zoya', 'Zora', 'zoy 'etc. Now, what can we do is turn the page of the phone book directly into the middle and let's say we end up at 'M'. Now we know that the name **John** will be definitely on the left part of the page. So we don't need the right part of the page anymore and can limit the search to the left side page.

Let's turn the page again into the middle and let's say it's 'H' now. We know that the name **John** definitely lies on the right half of the current part. So we can ignore the left half and limit the search to the right part.

So, every time we are restricting one-half of a page which makes binary search faster. If the phone book had **1000** pages we have to search just 10 times.

### Binary search in python
{% highlight python linenos %}
def binary_search(my_array, low, high, search_element):
    if low==high:
        return ("Not found")
    middle_index = int((low+high)/2)
    if my_array[middle_index] == search_element:
        return "found", middle_index
    elif my_array[middle_index]> search_element:
        high = middle_index -1
        return binary_search(my_array, low, high,
        search_element)
    elif my_array[middle_index] < search_element:
        low = middle_index +1
        return binary_search(my_array, low, high,
        search_element)

my_array = [1,2,3,4,5]
print(binary_search(my_array, 0, len(my_array)-1, 1))
{% endhighlight %}

### Conclusion
Binary search is faster than linear search. The time complexity for binary search is O(Log n) whereas the time complexity for linear search is O(n). If there are millions of names in the phone book, then the linear search will take a million searches in the worst case whereas the binary search will take only 20 searches.