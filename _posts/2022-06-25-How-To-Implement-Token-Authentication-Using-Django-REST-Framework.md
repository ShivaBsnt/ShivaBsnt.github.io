---
title: "How to Implement Token Authentication using Django REST Framework"
categories: programming
tags:
  - Python
  - Django
  - Django Rest Framework

header:
  teaser: "/assets/images/2022-06-25-How-To-Implement-Token-Authentication-Using-Django-REST-Framework/cover.png"
---

![Cover Page](/assets/images/2022-06-25-How-To-Implement-Token-Authentication-Using-Django-REST-Framework/cover.png)

## Setup the Rest API Project

### Step 1 : Install Django and Django Rest Framework

    pip install django
    pip install djangorestframework

### Step2. Create Django Project

    django-admin startproject TokenAuthentication

### Step3. Navigate to folder TokenAuthentication

    cd TokenAuthentication

### Step4. Start a new app

    python manage.py startapp authenticationdemo

### Step5. Add app to INSTALLED_APPS

The **authentictiondemo** (app that you created) and **rest_framework** (app that you installed) should be included in **INSTALLED_APPS** inside **settings.py** module.
{% highlight python linenos %}
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # Third party apps

    'rest_framework',

    # Local apps

    'authenticationdemo'
]
{% endhighlight %}

### Step6. Migrate the database

Run the command below from project root.

    python manage.py migrate

### Step7. Create Test API

Inside **_TokenAuthentication/authenticationdemo/views.py_**

{% highlight python linenos %}
from rest_framework.response import Response
from rest_framework.views import APIView

class APIVersion(APIView):
    def get(self, request, \*args, \*\*kwargs):
    return Response({
    'version': "0.1"
    })
{% endhighlight %}

### Step8. Register Path

Inside **_TokenAuthentication/urls.py_**
{% highlight python linenos %}
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/v1/', include('authenticationdemo.urls')),
]
{% endhighlight %}

Inside **_authenticationdemo/urls.py_**
{% highlight python linenos %}
from django.urls import path
from .views import APIVersion

urlpatterns = [
    path('version/', APIVersion.as_view())
]
{% endhighlight %}

### Step9. Check Endpoints

We have an endpoint **/api/v1/version/** where we can perform GET request. We can use browser to consume our API accessing the URL **http://127.0.0.1:8000/api/v1/version/**

![Result](/assets/images/2022-06-25-How-To-Implement-Token-Authentication-Using-Django-REST-Framework/result.png)

### Step10. Implement Permission to Protect API

Inside **_TokenAuthentication/settings.py_**
{% highlight python linenos %}
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated'
    ]
}
{% endhighlight %}

It we want to set a global permission class to all of our endpoints then we can use default permission. But if we want to protect our only specific view then we can define our permission class like this.

Inside **_TokenAuthentication/authenticationdemo/views.py_**

{% highlight python linenos %}
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from rest_framework.views import APIView
class APIVersion(APIView):
    permission_classes = [IsAuthenticated, ]

    def get(self, request, *args, **kwargs):
        return Response({
            'version': "0.1"
        })

{% endhighlight %}

Now, if we try to access the endpoints

![Result](/assets/images/2022-06-25-How-To-Implement-Token-Authentication-Using-Django-REST-Framework/result2.png)

### Step11. Implement Token Authentication
Add ***rest_framework.authtoken*** in ***INSTALLED_APPS*** inside **_TokenAuthentication/settings.py_** and include ***TokenAuthentication*** to ***REST_FRAMEWORK***.

{% highlight python linenos %}
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # Third party apps
    'rest_framework',
    'rest_framework.authtoken',
    # Local apps
    'authenticationdemo'
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication'
    ]
{% endhighlight %}

**Migrate** the database again to create the table that stores token.

    python manage.py migrate

### Step12. Token Request API
Since the user is unable to access our endpoints. We need API that provides user token.

Inside ***authenticationemo/urls.py**

{% highlight python linenos %}
from django.urls import path
from rest_framework.authtoken import views  # added views
from .views import APIVersion

urlpatterns = [
    path('login/', views.obtain_auth_token),  # added login url here
    path('version/', APIVersion.as_view())
]

{% endhighlight %}

Now create **super user**. Run the command below on project root.

    python manage.py createsuperuser

You will be asked for username and password. I will be using ***username*** as **admin** and ***password*** as **admin**.

![Result](/assets/images/2022-06-25-How-To-Implement-Token-Authentication-Using-Django-REST-Framework/superuser.png)

Now request for login with above superuser credential on URL **http://127.0.0.1:8000/api/v1/login/**

![Login](/assets/images/2022-06-25-How-To-Implement-Token-Authentication-Using-Django-REST-Framework/login.png)

**Note:**  ***We can also customize obtain_auth_token view***. Please refer to official django rest framework documentation for more details on customizing obtain_auth_token view. [CustomAuthToken](https://www.django-rest-framework.org/api-guide/authentication/#tokenauthentication))

### Now request API with token on the header
![API Version](/assets/images/2022-06-25-How-To-Implement-Token-Authentication-Using-Django-REST-Framework/apiversion.png)

#### Extra Tips

If we want to use ***Bearer*** instead of ***Token***.  Create a file **bearer.py** in ***TokenAuthentication***.


Inside **TokenAuthentication/bearer.py**

{% highlight python linenos %}
from rest_framework import authentication


class BearerAuthentication(authentication.TokenAuthentication):
    keyword = "Bearer"

{% endhighlight %}

Inside **_TokenAuthentication/settings.py_**

{% highlight python linenos %}
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'TokenAuthentication.bearer.BearerAuthentication',
    ]
}

{% endhighlight %}

Now it will accept ***Bearer*** instead of ***Token***.

![Bearer](/assets/images/2022-06-25-How-To-Implement-Token-Authentication-Using-Django-REST-Framework/bearer-token.png)