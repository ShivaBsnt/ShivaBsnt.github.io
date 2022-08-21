---
title: Extra Actions For Routing
categories: programming
tags:
  - Python
  - OOP

header:
  teaser: "/assets/images/2022-08-21-Extra-Actions-For-Routing/extra-action.png"
---

![Cover Page](/assets/images/2022-08-21-Extra-Actions-For-Routing/extra-action.png)

We can use **@action** decorator to add extra actions which can work for single object or an entire collection. In order to indicate the intend, we need to set **detail** argument which can be either **True** or **False** .
If we set **detail=True**, the router configures it's URL pattern accordingly and requires **pk** in URL patterns. 

**views.py**
{% highlight python linenos %}
from rest_framework import status
from rest_framework.decorators import action
from rest_framework.response import Response
from rest_framework.viewsets import ModelViewSet
from django.contrib.auth.models import User

from ExtraAction.serializers import UserSerializer, PasswordSerializer


class UserViewSet(ModelViewSet):
    queryset = User.objects.all()

    def get_serializer_class(self):
        if self.action == 'set_password':
            return PasswordSerializer
        return UserSerializer

    @action(detail=True, methods=['post'])
    def set_password(self, request, pk=None):
        user = self.get_object()
        serializer = self.get_serializer(data=request.data)
        if serializer.is_valid():
            user.set_password(serializer.validated_data['password'])
            user.save()
            return Response({'status': 'password set'})
        else:
            return Response(serializer.errors,
                            status=status.HTTP_400_BAD_REQUEST)

    @action(detail=False, methods=['get'])
    def recent_users(self, request):
        recent_users = User.objects.all().order_by('-last_login')

        page = self.paginate_queryset(recent_users)
        if page is not None:
            serializer = self.get_serializer(page, many=True)
            return self.get_paginated_response(serializer.data)

        serializer = self.get_serializer(recent_users, many=True)
        return Response(serializer.data)

{% endhighlight %}

`Note:``Action decorator routes GET request by default but it can also accept other HTTP methods by setting methods argument.`


**serializers.py**
{% highlight python linenos %}
from django.contrib.auth.models import User
from rest_framework import serializers
from rest_framework.serializers import ModelSerializer


class UserSerializer(ModelSerializer):
    class Meta:
        model = User
        fields = '__all__'


class PasswordSerializer(serializers.Serializer):
    password = serializers.CharField()

{% endhighlight %}

**urls.py**
{% highlight python linenos %}
from django.contrib import admin
from django.urls import path
from .views import UserViewSet
from rest_framework.routers import DefaultRouter
router = DefaultRouter()
router.register('user', UserViewSet, basename='user')


urlpatterns = [
    path('admin/', admin.site.urls)
] + router.urls

{% endhighlight %}

Now, the two action ***set_password*** and ***recent_users***  will be available at the url `^/user/1/set_password/` and `^/user/recent_users/`.

**Browsable API View**

![set_password action](/assets/images/2022-08-21-Extra-Actions-For-Routing/set-password.png)

![recent_users action](/assets/images/2022-08-21-Extra-Actions-For-Routing/recent-users.png)
