---
title: "Django Rest Framework Class Based Views"
categories: Programming
tags:
  - SQL
  - Python
  - Django

header:
  teaser: "/assets/images/2022-02-11-Django-Rest-Framework-Class-Based-Views/cover_page.png"
---

![Cover Page](/assets/images/2022-02-11-Django-Rest-Framework-Class-Based-Views/cover_page.png)
Instead of writing our API function based, we can write our API class based. The advantage it brings is a powerful pattern which allows us to reuse the common functionality keeping our code [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself).

**GitHub-Link For Project** : [https://github.com/ShivaBsnt/DjangoRestFramework.git](https://github.com/ShivaBsnt/DjangoRestFramework.git)

In previous blog we have used function based views to build our API. So we will use same **model** and **serializer** for our classed based views. In case you have missed it, here is the link.[(Django Rest Framework Function Based Views)](https://www.shivabahadurbasnet.com.np/programming/Django-Rest-Framework-Function-Based-Views/)

Let's have a glance at our model and serializer and quickly jump into views. Our URL will be slightly different than function based views as we are using class based views.

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

### URL

{% highlight python linenos %}
from django.urls import path
from .views import Student

urlpatterns = [
path('student/', Student.as_view(), name='student'),
]

{% endhighlight %}

### Class Based Views

Now, let's rewrite our API using class-based views.

{% highlight python linenos %}
from rest_framework.response import Response
from rest_framework.views import APIView
from Common.serializers import StudentSerializer
from Common.models import Student

class StudentAPIView(APIView):

"""List all students, or create a new student."""

    def get(self, request): # gets all student objects from database
        student = Student.objects.all() # sends queryset for serialization
        serializer = StudentSerializer(student, many=True) # sends data obtained from serializer into response
        return Response(serializer.data)

    def post(self, request):
        serializer = StudentSerializer(data=request.data)  # sends data requested for deserialization
        serializer.is_valid(raise_exception=True)# validates requested data
        serializer.save()# saves data to database
        return Response(serializer.data)# returns saved data as a response

{% endhighlight %}

Unlike function based views, we have not checked the methods of incoming request by ourself above but the request has been dispatched to appropriate function **.get()** and **.post()** defined by us. Magic!!!😲

![Cover Page](/assets/images/2022-02-11-Django-Rest-Framework-Class-Based-Views/magic.PNG)

The magic didn't happened by itself. Behind every great magic trick there is a tool hiding all the secrets. Likewise APIView here hides all the secret and lets magic to happen. Using an **APIView** is pretty much similar to using regular view as it inherits Django **View** class . All the incoming request is handled inside and at the end forwarded to appropriate function.

### Conclusion

Class based views are harder to read and understand sometime as it hides code in parent classes but it comes with great advantage of code reusability and extendibility. Likewise, it supports Object oriented techniques such as mixins( multiple inheritance). But saying that class based views is always the superior is a great mistake.

### References

1. more, R., 2022. Class-Based Views vs. Function-Based Views. [online] Simple is Better Than Complex. Available at: <https://simpleisbetterthancomplex.com/article/2017/03/21/class-based-views-vs-function-based-views.html> [Accessed 10 February 2022].
2. Christie, T., 2022. Views - Django REST framework. [online] Django-rest-framework.org. Available at: <https://www.django-rest-framework.org/api-guide/views/> [Accessed 12 February 2022].
3. Christie, T., 2022. 3 - Class based views - Django REST framework. [online] Django-rest-framework.org. Available at: <https://www.django-rest-framework.org/tutorial/3-class-based-views/> [Accessed 12 February 2022].
