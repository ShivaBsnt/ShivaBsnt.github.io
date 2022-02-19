---
title: "Generic Views-Django Rest Framework"
categories: Programming
tags:
  - Python
  - Django
  - Django Rest Framework
  - Generic Views


header:
  teaser: "/assets/images/2022-02-19-Generic-Views-Django-Rest-Framework/cover.png"
---

![Cover Page](/assets/images/2022-02-19-Generic-Views-Django-Rest-Framework/cover.png)
Generic views are designed as a shortcut for common usage patterns.The common patterns used while building views is abstracted so that we can build our common views quickly without having to repeat code ourself.

**Let's suppose that our requirement is to create an api where we can post student data and get list of all the student's data.**

First thing first, let's build **model** and **serializer**.
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

***Before using generics views to solve our problem, let's look at function based and simple class based approach of solving it.***

### Function Based View
{% highlight python linenos %}
@api_view(['GET', 'POST'])
def get_post_student(request):
    if request.method.lower() == 'get':
        # gets all student objects from database
        student = Student.objects.all()
        # sends queryset for serialization
        serializer = StudentSerializer(student, many=True)
        # sends data obtained from serializer into response
        return Response(serializer.data)

    if request.method.lower() == 'post':
        # sends data requested for deserialization
        serializer = StudentSerializer(data=request.data)
        # validates requested data
        serializer.is_valid(raise_exception=True)
        # saves data to database
        serializer.save()
        # returns saved data as a response
        return Response(serializer.data)

{% endhighlight %}

### Class Based View
{% highlight python linenos %}
class StudentAPIView(APIView):
    def get(self, request): 
        # gets all student objects from database
        student = Student.objects.all() 
        # sends queryset for serialization
        serializer = StudentSerializer(student, many=True) 
        # sends data obtained from serializer into response
        return Response(serializer.data)

    def post(self, request):
        # sends data requested for deserialization
        serializer = StudentSerializer(data=request.data) 
        # validates requested data
        serializer.is_valid(raise_exception=True)
        # saves data to database
        serializer.save()
        # returns saved data as a response
        return Response(serializer.data)

{% endhighlight %}

We saw the class based and function based views above to solve our problem. But what our generics views will look like? Excited? Let's meet our generic view.  

### Generic View
{% highlight python linenos %}
from rest_framework.generics import ListAPIView,\
    CreateAPIView
from Common.models import Student
from Common.serializers import StudentSerializer

class StudentGenericListAPIView(ListAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentSerializer

class StudentGenericCreateAPIView(CreateAPIView):
    queryset = Student.objects.all()
    serializer_class = StudentSerializer

{% endhighlight %}

Is this your first meet with **generic**? If the answer is **yes**!, you might have found him really confusing. But don't worry generic was confusing to me too when i first met him. I had a lots of question like

- **Does this view even work?** 
- **Don't we have to check the incoming request method?**
- **Don't we have to send data for serialization or de-serialization?**
- **And where is the response?** 
- **Don't we have to send response to the front end?**

The single word answer to all the above question is **yes** but its not us who does that all now, it's the generic view that handles all the common patterns in views.

***We have been talking about common patterns a lot, so what is common pattern? Can we identify any common pattern from class based view and function based view?***

![function based view vs class based view](/assets/images/2022-02-19-Generic-Views-Django-Rest-Framework/fbvcbv.PNG)

***To understand it more clearly let's look at a views to create an API where we can post teacher data instead of student and get list of all the teacher's data.***

![comparison](/assets/images/2022-02-19-Generic-Views-Django-Rest-Framework/comparison.PNG)

**Now we know that everything is common except for those highlighted ones.**

**So, what are the uncommon ones above?**

1) Model object is different. (**Teacher** instead of **Student**)

2) Serializer class is different. (**TeacherSerializer** instead of **StudentSerializer**)

**And what are the common patterns?**

**Common Patterns in GET**

1) Getting all the objects of related model.

2) Sending queryset for serialization.

3) Returning data from serializer into response.

**Common Patterns in POST**

1) Sending requested data for de-serialization

2) Checking if the requested data is valid.

3) Saving the validated data.

4) Returning data from serializer into response.

![generic views](/assets/images/2022-02-19-Generic-Views-Django-Rest-Framework/generic.PNG)

**Conclusion:** That's why generic views has attributes queryset and serializer_class to take the **uncommon** ones and generate all the common patterns under the hoods so that we do not have to repeat the code, allowing us to build an api quickly.


