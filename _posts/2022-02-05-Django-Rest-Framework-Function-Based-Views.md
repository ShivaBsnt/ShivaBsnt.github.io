---
title: "Django Rest Framework Function Based Views"
categories: Programming
tags:
  - SQL
  - Python
  - Django

header:
  teaser: "/assets/images/2022-02-05-Django-Rest-Framework-Function-Based-Views/cover.png"
---

![Cover Page](/assets/images/2022-02-05-Django-Rest-Framework-Function-Based-Views/cover.png)
Rest framework provides simple set of decorators for regular Django function to wrap around, allowing function to receive the instance of **Request** (rather than the django HttpRequest) and return **Response** (instead of Django HttpResponse ) whereas Class-based views contains **APIView** which is a sub class of Django view.

**GitHub-Link For Project** : [https://github.com/ShivaBsnt/DjangoRestFramework.git](https://github.com/ShivaBsnt/DjangoRestFramework.git)

Let's make model, URL, serializer and quickly and jump straight to the views.

### Model

{% highlight python linenos %}
from django.db import models

class Student(models.Model):
    first_name = models.CharField(max_length=10)
    last_name = models.CharField(max_length=10)
    email = models.EmailField()
    age = models.PositiveSmallIntegerField()
    number = models.CharField(max_length=15)


{% endhighlight %}

### Serializer

{% highlight python linenos %}
from django.db import models
from rest_framework import serializers

from Common.models import Student

class StudentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student
        fields = "__all__"


{% endhighlight %}

### Url

{% highlight python linenos %}
path('student/', get_post_student, name='student'),"
{% endhighlight %}

### Function Based Views

Now, we will use function based view to get and create a student.

{% highlight python linenos %}
@api_view(['GET', 'POST'])
def get_post_student(request):
if request.method.lower() == 'get':
serializer = StudentSerializer(Student.objects.all(), many=True)
return Response(serializer.data)

    if request.method.lower() == 'post':
        serializer = StudentSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data)

{% endhighlight %}

##### Let's break each line of code to understand what our views does.

    @api_view(['GET', 'POST'])


The api_view decorator in this code is accepting a list of methods that view should respond to. The function **get_post_student** will only accept **GET** and **POST** request in this case.

**_Note: By default the api_view will only accept a get request. If we do not provide any http methods then the function responds to only get methods. It means that writing api_view() and api_view(['GET']) is same._**

### 1. GET

    if request.method.lower() == 'get':


Since the function accepts both **GET** as well as **POST** request, so we are checking the methods before performing any action.

    serializer = StudentSerializer(Student.objects.all(), many=True)


**StudentSerializer** has been used as a serializer for **Student** objects. It allows complex data types such as **queryset** to be easily converted into **JSON, XML** or other content types.

![Confuse Image](/assets/images/2022-02-05-Django-Rest-Framework-Function-Based-Views/confuse.png)

What the heck is serializer? Why do we even need serializer? Confused right? No problem let's look at the figure below.

![Serializer Infographics Image](/assets/images/2022-02-05-Django-Rest-Framework-Function-Based-Views/serializer.png)

**JSON** and **XML** is not understood by python directly. So they need to go through serializer to get converted into python native data types. Similarly python native data types is also not understood by JSON or XML so they need to be also converted into their own format. **_In lay man term, serializer converts the data types from one format to another and validates the incoming data from JSON, XML or any other format._**

Have you noticed that we have used **many=True** above? It is because **Student.objects.all()** returns a **queryset**. **Queryset** is nothing but a list of objects.

    <!-- Importing Student model and getting all of its object -->
    >>> from Common.models import Student
    >>> Student.objects.all()
    <!-- result of above orm -->
    <QuerySet [<Student: Student object (2)>]>
    >>>

Since **Student.objects.all()** does not return a single object, it returns a list of objects therefore **many** is set to **True**.


    return Response(serializer.data)


Here, the data received from serializer is dictionary which is further converted into format like **JSON** or **XML** and then returned to an end from where it is requested.

### 2. POST

    if request.method.lower() == 'post':


Like get request, request method is checked here to carry any further action.

    serializer = StudentSerializer(data=request.data)

The data received from front end is sent through serializer for validation. It checks whether the data required for backend matches the frontend data request.But validation does not runs here.

    serializer.is_valid(raise_exception=True)

The validation starts once **is_valid()** method is called. The **raise_exception=True** raises a validation error and returns suitable response if validation does not matches. For e.g our model **Student** has got field **age** which
is an integer. If request sent has got an age in **float** or **string** then our validation does not matches and serializer throws an exception.


    serializer.save()


The data validated from serializer gets saved here to database. The serializer returns an instance of validated object which is saved here.


    return Response(serializer.data)


This line sends a data saved to database with suitable response.

### Conclusion
We have successfully built an api using function based views to **get** and **post** the student and learned about serializer.

<------------------------ HAPPY CODING ------------------------>