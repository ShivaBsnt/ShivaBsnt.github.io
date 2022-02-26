---
title: "Django Rest Framework Mixins"
categories: Programming
tags:
  - Python
  - Django
  - Django Rest Framework
  - Mixins

header:
  teaser: "/assets/images/2022-02-26-Django-Rest-Framework-Mixins/mixins.png"
---

![Cover Page](/assets/images/2022-02-26-Django-Rest-Framework-Mixins/mixins.png)
The mixin classes provide the actions that are used to provide the basic view behavior. Note that the mixin classes provide action methods rather than defining the handler methods, such as .get() and .post(), directly. This allows for more flexible composition of behavior.-[Official Django Rest framework documentation](https://www.django-rest-framework.org/api-guide/generic-views/#mixins)

**_If you have used generic views before, then you have already used mixins. How? Let me show you._**

### Example of Generic View

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

**The code above shows the generic way of building an API but still mixins are nowhere to be seen? 😕 Where is the mixins?**

Let's dig a little deep and see how generics are built. May be will find our answer there.

{% highlight python linenos %}
class CreateAPIView(mixins.CreateModelMixin,
                    GenericAPIView):
    """
    Concrete view for creating a model instance.
    """
    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)
{% endhighlight %}

{% highlight python linenos %}
class ListAPIView(mixins.ListModelMixin,
                  GenericAPIView):
    """
    Concrete view for listing a queryset.
    """
    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)
{% endhighlight %}

There you are mixins laying under the hoods. So generic views are also nothing but the product of multiple inheritance where it inherits classes from **mixins** with **GenericAPIView**.

**Let's dig a bit and see the mixins classes.**

{% highlight python linenos %}
class CreateModelMixin:
    """
    Create a model instance.
    """
    def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        self.perform_create(serializer)
        headers = self.get_success_headers(serializer.data)
        return Response(serializer.data, status=status.HTTP_201_CREATED, headers=headers)

    def perform_create(self, serializer):
        serializer.save()

    def get_success_headers(self, data):
        try:
            return {'Location': str(data[api_settings.URL_FIELD_NAME])}
        except (TypeError, KeyError):
            return {}

{% endhighlight %}

{% highlight python linenos %}
class ListModelMixin:
    """
    List a queryset.
    """
    def list(self, request, *args, **kwargs):
        queryset = self.filter_queryset(self.get_queryset())

        page = self.paginate_queryset(queryset)
        if page is not None:
            serializer = self.get_serializer(page, many=True)
            return self.get_paginated_response(serializer.data)

        serializer = self.get_serializer(queryset, many=True)
        return Response(serializer.data)
{% endhighlight %}

{% highlight python linenos %}
class RetrieveModelMixin:
    """
    Retrieve a model instance.
    """
    def retrieve(self, request, *args, **kwargs):
        instance = self.get_object()
        serializer = self.get_serializer(instance)
        return Response(serializer.data)

{% endhighlight %}

{% highlight python linenos %}
class UpdateModelMixin:
    """
    Update a model instance.
    """
    def update(self, request, *args, **kwargs):
        partial = kwargs.pop('partial', False)
        instance = self.get_object()
        serializer = self.get_serializer(instance, data=request.data, partial=partial)
        serializer.is_valid(raise_exception=True)
        self.perform_update(serializer)

        if getattr(instance, '_prefetched_objects_cache', None):
            # If 'prefetch_related' has been applied to a queryset, we need to
            # forcibly invalidate the prefetch cache on the instance.
            instance._prefetched_objects_cache = {}

        return Response(serializer.data)

    def perform_update(self, serializer):
        serializer.save()

    def partial_update(self, request, *args, **kwargs):
        kwargs['partial'] = True
        return self.update(request, *args, **kwargs)

{% endhighlight %}

{% highlight python linenos %}
class DestroyModelMixin:
    """
    Destroy a model instance.
    """
    def destroy(self, request, *args, **kwargs):
        instance = self.get_object()
        self.perform_destroy(instance)
        return Response(status=status.HTTP_204_NO_CONTENT)

    def perform_destroy(self, instance):
        instance.delete()

{% endhighlight %}

**Conclusion:** So what mixins offer is action. The action like **list**, **retrieve**, **create**, **update** and **destroy**. The behaviour we use to define inside simple function based view and class based api view is now defined by mixins itself. We can say that the common patterns generic views support without having to write code by developer themselves is mixins under the hoods and to be more specific "**actions**".
