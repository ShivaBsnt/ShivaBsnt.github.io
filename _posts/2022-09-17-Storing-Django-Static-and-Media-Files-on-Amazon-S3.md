---
title: How to Upload Files to AWS S3 using the
 Django Rest Framework?
categories: web development
tags:
  - Python
  - DRF
  - DjangoRestFramework
  - AWS
  - S3
  - bucket

header:
  teaser: "/assets/images/2022-09-17-Storing-Django-Static-and-Media-Files-on-Amazon-S3/django-with-s3.png"
---

![Cover Page](/assets/images/2022-09-17-Storing-Django-Static-and-Media-Files-on-Amazon-S3/django-with-s3.png)
## Dependencies
We will need two python libraries.
    
    Boto3
    django-storages

In order to install the dependency package run the command below:

    pip install boto3
    pip install django-storages

Now include **storages** on INSTALLED_APPS inside the **settings.py**
{% highlight python linenos %}
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'Django.contrib.gis',
    ‘storages’
]
{% endhighlight %}

Create a new python file **storage_backends.py** (if you want a different name it's completely fine). You can place it anywhere but i recommend placing  it at the level of **settings.py**.

## storage_backends.py

{% highlight python linenos %}

from django.conf import settings
from storages.backends.s3boto3 import S3Boto3Storage

# class for static files
class StaticS3Storage(S3Boto3Storage):
    location = settings.AWS_STATIC_LOCATION

# class for public media
class PublicMediaS3Storage(S3Boto3Storage):
    location = settings.AWS_PUBLIC_MEDIA_LOCATION
    file_overwrite = False

# class for private media
class PrivateMediaS3Storage(S3Boto3Storage):
    location = settings.AWS_PRIVATE_MEDIA_LOCATION
    default_acl = 'private'
    file_overwrite = False
    custom_domain = False

{% endhighlight %}

The **public** media and **static** files can be accessd by anyone with **links** or **urls** but **private** media is protected and cannot be accessed with url only. Trying to access private media without credential will result into
**"Access Denied"** error. You can keep the media private or public according to your project needs.

## env.py
{% highlight python linenos %}

AWS_ACCESS_KEY_ID = '****************'
AWS_SECRET_ACCESS_KEY = '************************'
AWS_STORAGE_BUCKET_NAME = '**********************'
AWS_S3_CUSTOM_DOMAIN = '%s.s3.amazonaws.com' % AWS_STORAGE_BUCKET_NAME

AWS_S3_OBJECT_PARAMETERS = {
    'CacheControl': 'max-age=86400',
}


AWS_STATIC_LOCATION = 'static'
STATICFILES_STORAGE = 'mysite.storage_backends.StaticStorages3'
STATIC_URL = "https://%s/%s/" % (AWS_S3_CUSTOM_DOMAIN, AWS_STATIC_LOCATION)


AWS_PUBLIC_MEDIA_LOCATION = 'media/public'
DEFAULT_FILE_STORAGE = 'myapp.storage_backends.PublicMediaS3Storage'

AWS_PRIVATE_MEDIA_LOCATION = 'media/private'
PRIVATE_FILE_STORAGE = 'myapp.storage_backends.PrivateMediaS3Storage'

{% endhighlight %}

The credential AWS ACCESS KEY ID, AWS SECRET ACCESS KEY and AWS STORAGE BUCKET NAME should be obtained from AWS.

Now run the command "python manage.py collectstatic". It will collect our static files to remote location as per our new settings. And from now, static and media files will be served from **AWS S3**.

<---------------------------- Happy Coding ---------------------------->