---
title: "ModelViewSet Django Rest Framework"
categories: DRF
tags:
  - Python
  - Django
  - Django Rest Framework
  - ModelViewSet

header:
  teaser: "/assets/images/2022-03-05-Django-Rest-Framework-Model-View-Set/model-viewset.png"
---

![Cover Page](/assets/images/2022-03-05-Django-Rest-Framework-Model-View-Set/model-viewset.png)
Model viewset comes with default action `create()`, `retrieve()`, `update()`,
`partial_update()`, `destroy()` and `list()`. All these action are the result of multiple inheritance of **mixins**. By the same, it supports **DefaultRouter** from rest framework **routers** providing a huge advantage as we no longer have to deal with writing URL configuration ourselves.

**Now the real question is when does the model viewset becomes handy?🤔😕**

**📌Suppose that we need to build an API where we can create, read, update and delete student.**

***How would you make an API? By using a function based view? By using APIView of class based view? By Using generic view? or By using viewset?***

Any of the **view** above can give us an API to create, read, update and delete the student. But the appropriate solution would be using a **model viewset**.✔️ Writing a code with a function based view `apiview()` and class based `APIView` in this case would result into long lines of codes. We have to write each line of codes and configure our URL for all the methods by ourselves. Similarly, generics is also not an appropriate solution as it requires two views **ListCreateAPIView** and **RetrieveUpdateDestroyAPIView** to handle all the **CRUD** operation. On top of that, it also requires URL configuration. So model viewset is a perfect fit to this case as it can handle all the url for each action by itself and does not require two or more views like generics.

### Now let's build and API with ModelViewSet
#### Model
{% highlight python linenos %}
from django.db import models


class Student(models.Model):
    first_name = models.CharField(max_length=10)
    last_name = models.CharField(max_length=10)
    email = models.EmailField()
    age = models.PositiveSmallIntegerField()
    number = models.CharField(max_length=15)

{% endhighlight %}

#### Views
{% highlight python linenos %}
from rest_framework import viewsets

from Common.serializers import StudentSerializer
from Common.models import Student


class StudentViewSet(viewsets.ModelViewSet):
    serializer_class = StudentSerializer
    queryset = Student.objects.all()

{% endhighlight %}

#### URL
{% highlight python linenos %}
from rest_framework.routers import DefaultRouter
from .views import StudentViewSet
r = DefaultRouter()
r.register('student', StudentViewSet, basename='student')

urlpatterns = [
] + r.urls

{% endhighlight %}

In **views**, just two lines of code **serializer_class** and **queryset** in model viewset build an entire API and fulfilled the requirement. Now it creates all the action by itself using provided queryset and serializer class. If we dig little deep, all those actions are from mixins.

{% highlight python linenos %}
class ModelViewSet(mixins.CreateModelMixin,
                   mixins.RetrieveModelMixin,
                   mixins.UpdateModelMixin,
                   mixins.DestroyModelMixin,
                   mixins.ListModelMixin,
                   GenericViewSet):
    """
    A viewset that provides default `create()`, `retrieve()`, `update()`,
    `partial_update()`, `destroy()` and `list()` actions.
    """
    pass

{% endhighlight %}

**Conclusion:** ModelViewset is a viewset for CRUD activities on data model that is relatively simple to configure. If we want to build a REST API for an object defined in our models, ModelViewSet is the quickest method to expose our API.

