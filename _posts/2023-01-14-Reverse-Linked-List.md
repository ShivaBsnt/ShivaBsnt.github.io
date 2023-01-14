---
title: Reverse Linked List in Python (Iterative Method)
categories: DataStructure Algorithm
tags:
  - Python
  - Data Structure
  - Algorithm

header:
  teaser: "/assets/images/2023-01-14-Reverse-Linked-List/reverse_linked_list.png"
---

![Cover Page](/assets/images/2023-01-14-Reverse-Linked-List/reverse_linked_list.png)

### Linked List

A linked list is a linear data structure that includes a series of connected nodes. Here, each node stores the data and the address of the next node. The elements are not stored at contiguous memory locations.

#### Example 1:

![Example1](/assets/images/2023-01-14-Reverse-Linked-List/input1.jpg)

    Input: head = [1,2,3,4,5]
    Output: [5,4,3,2,1]

#### Example 2:

![Example2](/assets/images/2023-01-14-Reverse-Linked-List/input2.jpg)

    Input: head = [1,2]
    Output: [2,1]

#### Example 3:

    Input: head = []
    Output: []

### Steps to solve the problem:

1.  Initialize the two pointers **prev** and **current** (Initially point prev to **None** and current to **head** of linked list).

        prev = None
        current = head

2.  Iterate through the linked list in a loop until and unless **current is not None** and do the following:

    a. Declare a **temp** variable. (It is used to store the next value of current node)

          temp = current.next

    b. Point next of **current to prev**.

          current.next = prev

    c. Set **prev** as **current**.

            prev = current

    d. Set **current** as **temp**.

            current = temp

### Code Implementation

{% highlight python linenos %}

# example input -> 1->2->->3->4->5

def reverseList(head): # prev = None
prev = None # current = 1
current = head

    # current = 1 is not None? True
    # current = 2 is not None? True
    # current = 3 is not None? True
    # current = 4 is not None? True
    # current = 5 is not None? True
    # current = None is not None? False (loop->breaks)

    while current is not None:

        temp = current.next
        #1.next=2, 2.next=3, 3.next=4, 4.next=5, 5.next=None

        current.next = prev
        #1.next=None, 2.next=1, 3.next=2, 4.next=3, 5.next=4
        #None<-1, 1<-2, 2<-3, 3<-4, 4<-5

        prev = current
        #prev=1, prev=2, prev=3, prev=4, prev=5

        current = temp
        #current=2, current=3, current=4, current=5,
        #current=None

    return prev

{% endhighlight %}
