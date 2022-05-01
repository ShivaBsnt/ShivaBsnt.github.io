---
title: "Serializer - Django Rest Framework"
categories: Programming
tags:
  - Python
  - Django
  - Django Rest Framework
  - Serializer

header:
  teaser: "/assets/images/2022-04-02-Serializer-Django-Rest-Framework/cover.png"
---

![Cover Page](/assets/images/2022-04-02-Serializer-Django-Rest-Framework/cover.png)
Serializer in Django Rest Framework converts complex objects into data types understandable by front-end frameworks. It allows the python native data types to be rendered into **JSON**, **XML**, or other content types. Serializer also provides deserialization, which allows the parsed data to be converted back into complex data types only after validating the incoming data.

Before moving further, my friend **“SB2”** wants to meet you and has something to say.

![Robot Speaking](/assets/images/2022-04-02-Serializer-Django-Rest-Framework/robo1.png)

What message did SB2 deliver to you guys? Was it interesting? Was it motivating? What was he trying to say?😕😵 Did you guys respond to him with a message that he could understand? Here, the SB2 is speaking in Sanskrit “**प्राता रत्नं प्रातरित्वा दधाति।**” which means “**An early riser earns good health.**”

The same thing goes with the python. It cannot understand the data types of other languages. It needs data in its own native data types to store and understand else it makes no sense to python. Similarly, when python delivers its native and complex data types to other languages, it makes no sense to them as well.

**So, what is the solution? Who will save us from the problem?**
![Seralizer](/assets/images/2022-04-02-Serializer-Django-Rest-Framework/serializer.png)


**Now, let's build a simple API and declare a serializer class.**
### Model
{% highlight python linenos %}
from django.db import models


class Comment(models.Model):
    email = models.EmailField()
    subject = models.CharField(max_length=250)
    created_at = models.DateTimeField(auto_now_add=True)

{% endhighlight %}

### Serializer
{% highlight python linenos %}

from rest_framework import serializers


class CommentSerializer(serializers.Serializer):
    email = serializers.EmailField()
    subject = serializers.CharField(max_length=250)


{% endhighlight %}

### View
{% highlight python linenos %}

from rest_framework.viewsets import ModelViewSet
from .models import Comment
from .serializer import CommentSerializer


class CommentViewSet(ModelViewSet):
    queryset = Comment.objects.all()
    serializer_class = CommentSerializer

{% endhighlight %}

### URL
{% highlight python linenos %}

from django.contrib import admin
from django.urls import path
from app1.views import CommentViewSet
from rest_framework.routers import DefaultRouter
r = DefaultRouter()
r.register('', CommentViewSet, basename='comment')
urlpatterns = [
    path('admin/', admin.site.urls),
]+r.urls

{% endhighlight %}

Let’s add some of the comments using postman. At first, let’s send invalid data.

![Seralizer](/assets/images/2022-04-02-Serializer-Django-Rest-Framework/serializer-validation.png)

Here, the serializer validates the data before storing it in a database. We have defined in our serializer that the email field should be **email**, not a **character** or **text**.

![Seralizer](/assets/images/2022-04-02-Serializer-Django-Rest-Framework/validation1.png) 
So, the serializer validates the incoming data and prevents invalid data from storing into the database.

Similarly, let's add invalid data to the subject and see how our serializer reacts.

![Seralizer](/assets/images/2022-04-02-Serializer-Django-Rest-Framework/serializer-validation2.png) 

In serializer, we have defined that our subject must be no longer than **250** characters.

![Seralizer](/assets/images/2022-04-02-Serializer-Django-Rest-Framework/validation2.png) 

Therefore serializer is again preventing invalid data and performing validation on every **POST** request.

**NOW, LET'S ADD SOME VALID COMMENTS**

![Seralizer](/assets/images/2022-04-02-Serializer-Django-Rest-Framework/valid-data.png)
We can see data on **GET** requests which means that we have added valid data to our database. But as said earlier, python stores data in its native data type, so let’s see if the data is converted into its complex data type or not.

![Seralizer](/assets/images/2022-04-02-Serializer-Django-Rest-Framework/complex-data-type.png)

We can see that the comment posted has been deserialized and changed into a **QuerySet**.
QuerySet refers to the list of objects. It is a complex as well as python native data type. The other frontend technologies do not understand such data types, thus the serializer converts such data types into a dictionary and sends them into a format that the front end can understand.
