---
title: "Permissions in Django REST Framework"
categories: "DRF"
tags:
  - "Permissions"
  - "has_permission"
  - "has_object_permission"
  - "check_permissions"
  - "check_object_permissions"
  - "Custom Permissions"
  - "Built-in Permissions"
  - "Django REST Framework"

header:
  teaser: "/assets/images/2025-10-04-Validation-in-Django-REST-Framework-Serializers/teaser.png"
---
![Validation in django restframework cover page](/assets/images/2025-10-04-Validation-in-Django-REST-Framework-Serializers/cover.png)

# Introduction
Permissions are means to **grant or deny access for different classes of users** to different parts of the API. It runs at the very start of the view, before any other code is allowed to proceed. Since **authentication by itself won't allow or disallow an incoming request**, the entity requesting access must go through permissions checks to gain information or code.


## View Permissions
There are two methods in **APIView** [(rest_framework/views.py)](https://github.com/encode/django-rest-framework/blob/main/rest_framework/views.py) that check for permissions:

1. **check_permissions** checks if the request should be permitted **based on the request**

    {% highlight python linenos %}
    def check_permissions(self, request):
        """
        Check if the request should be permitted.
        Raises an appropriate exception if the request is not permitted.
        """
        for permission in self.get_permissions():
            if not permission.has_permission(request, self):
                self.permission_denied(
                    request,
                    message=getattr(permission, 'message', None),
                    code=getattr(permission, 'code', None)
                )
    {% endhighlight %}

    The **check_permission method** is called before the view handler is executed inside the **initial** method.
    
    {% highlight python linenos %}
    def initial(self, request, *args, **kwargs):
        """
        Runs anything that needs to occur prior to calling the method handler.
        """
        self.format_kwarg = self.get_format_suffix(**kwargs)

        # Perform content negotiation and store the accepted info on the request
        neg = self.perform_content_negotiation(request)
        request.accepted_renderer, request.accepted_media_type = neg

        # Determine the API version, if versioning is in use.
        version, scheme = self.determine_version(request, *args, **kwargs)
        request.version, request.versioning_scheme = version, scheme

        # Ensure that the incoming request is permitted
        self.perform_authentication(request)
        self.check_permissions(request) # <- method is called here
        self.check_throttles(request)
    {% endhighlight %}

2. **check_object_permissions** checks if the request should be permitted **based on the request and the object**

    {% highlight python linenos %}
    def check_object_permissions(self, request, obj):
        """
        Check if the request should be permitted for a given object.
        Raises an appropriate exception if the request is not permitted.
        """
        for permission in self.get_permissions():
            if not permission.has_object_permission(request, self, obj):
                self.permission_denied(
                    request,
                    message=getattr(permission, 'message', None),
                    code=getattr(permission, 'code', None)
                )
    {% endhighlight %}

    The **check_object_permissions** method is not executed unless it is called explicitly.

#### check_object_permissions in APIView

{% highlight python linenos %}
class ExampleAPIView(APIView):

def get(self, request, pk):
    example = get_object_or_404(Example.objects.all(), pk=pk)
    self.check_object_permissions(request, example) #<- method is explicitly called here
    serializer = ExampleSerializer(message)
    return Response(serializer.data)
{% endhighlight %} 

#### check_object_permissions in GenericAPIView and GenericViewSet

In GenericAPIView, the **check_object_permissions** method is called inside the **get_object** method after the object is retrieved from the database.

{% highlight python linenos %}
class GenericAPIView(views.APIView):
    def get_object(self):
        """
        Returns the object the view is displaying.

        You may want to override this if you need to provide non-standard
        queryset lookups.  Eg if objects are referenced using multiple
        keyword arguments in the url conf.
        """
        queryset = self.filter_queryset(self.get_queryset())

        # Perform the lookup filtering.
        lookup_url_kwarg = self.lookup_url_kwarg or self.lookup_field

        assert lookup_url_kwarg in self.kwargs, (
            'Expected view %s to be called with a URL keyword argument '
            'named "%s". Fix your URL conf, or set the `.lookup_field` '
            'attribute on the view correctly.' %
            (self.__class__.__name__, lookup_url_kwarg)
        )

        filter_kwargs = {self.lookup_field: self.kwargs[lookup_url_kwarg]}
        obj = get_object_or_404(queryset, **filter_kwargs)

        # May raise a permission denied
        self.check_object_permissions(self.request, obj) # <- method is called here

        return obj
{% endhighlight %}

Now, since the class **GenericViewSet** inherits **ViewSetMixin** and **GenericAPIView**, **GenericViewSet(ViewSetMixin, generics.GenericAPIView)** also calls the **check_object_permissions** method inside the **get_object** method.

## Permission Configuration
There are two ways of setting permissions in Django Rest Framework:
1. **Global Permission Policy**
    
A rule that applies to **every API view** in our project unless we override it.

**Example:**

If we set this in **settings.py**:
{% highlight python linenos %}
    REST_FRAMEWORK = 
    {
        'DEFAULT_PERMISSION_CLASSES':
        [
            'rest_framework.permissions.IsAuthenticated',
        ]
    }
{% endhighlight %}

Then all APIs will require authentication by default.
    
2. **View-Specific Permission Policy**

A rule that applies only to a **particular API view or endpoint**, not the whole project.

{% highlight python linenos %}
class ExampleAPIView(APIView):
    permission_classes = [AllowAny]
{% endhighlight %}

Here, even if the global policy requires authentication, this view overrides it.

## Permission Classes
Permissions in DRF are defined as a list of permission classes. We can either create our own or use one of the seven [built-in classes](https://www.django-rest-framework.org/api-guide/permissions/#api-reference). All permission classes, either custom or built-in, extend from the **BasePermission** class:

{% highlight python linenos %}
class BasePermission(metaclass=BasePermissionMetaclass):
    """
    A base class from which all permission classes should inherit.
    """

    def has_permission(self, request, view):
        """
        Return `True` if permission is granted, `False` otherwise.
        """
        return True

    def has_object_permission(self, request, view, obj):
        """
        Return `True` if permission is granted, `False` otherwise.
        """
        return True
{% endhighlight %}

Each permission class overrides one or both methods of **BasePermission** to conditionally return **True**. When a method returns **True**, access is granted; otherwise, access is denied.

Those two methods in **BasePermission** are:

1. **has_permission**

Checks if the request and user has access to the view in general (not tied to any specific object).

The **has_permission** method is invoked through the **check_permissions** method, which runs the **has_permission** method of all permission classes defined in the view.

2. **has_object_permission**

Checks if the request and the user have permission to interact with a specific object.


The **has_object_permission** method is invoked through the **check_object_permissions** method, which runs the **has_object_permission** method of all permission classes defined in the view.

## How Permissions Work in DRF
When a request hits a DRF API view:

1. Authentication happens first

    If authentication succeeds:
    - **request.user** is set to the authenticated user object.
    - **request.auth** is set to any additional authentication data (like a token).

    If authentication fails:
    - **request.user** becomes an **AnonymousUser** (instance of **django.contrib.auth.models.AnonymousUser**).
    - **request.auth** is set to **None**.

2. Permission checks run next

    DRF runs the **has_permission** or **has_object_permission** method of each permission class listed in the view (or globally).

    If permission succeeds:
    
    - The view logic executes (e.g., get(), post(), etc.).

    If permission fails:

    - The request stops immediately with an **exceptions.PermissionDenied** or **exceptions.NotAuthenticated**, and DRF returns a "**403 Forbidden**" or "**401 Unauthorized**" response.


## Built-in Permission Classes
With regard to the built-in DRF permission classes, all of them override has_permission while only DjangoObjectPermissions overrides has_object_permission:

| **Permission Class** | **has_permission()** | **has_object_permission()** |
|------------------------|----------------------|------------------------------|
| **AllowAny** | ✅ Yes | ❌ No |
| **IsAuthenticated** | ✅ Yes | ❌ No |
| **IsAdminUser** | ✅ Yes | ❌ No |
| **IsAuthenticatedOrReadOnly** | ✅ Yes | ❌ No |
| **DjangoModelPermissions** | ✅ Yes | ❌ No |
| **DjangoModelPermissionsOrAnonReadOnly** | ✅ Yes | ❌ No |
| **DjangoObjectPermissions** | ✅ Yes | ✅ Yes |

## Custom Permission Classes
The built-in permission classes aren’t enough for project-specific requirements. In such cases, DRF allows us to create custom permission classes by inheriting from **BasePermission**. These classes let us define our own access rules using the **has_permission** and **has_object_permission** methods.

{% highlight python linenos %}
from rest_framework.permissions import BasePermission, SAFE_METHODS

class IsOwnerOrReadOnly(BasePermission):
    """
    Only the object owner can edit or delete, others can only view.
    """
    def has_object_permission(self, request, view, obj):
        if request.method in SAFE_METHODS:
            return True  # Anyone can read
        return obj.owner == request.user  # Only owner can modify
{% endhighlight %}

{% highlight python linenos %}
 class ExampleAPIView(APIView):
    permission_classes = [IsOwnerOrReadOnly] # <- custom permission here
    def get(self, request, pk):
        example = get_object_or_404(Example.objects.all(), pk=pk)
        self.check_object_permissions(request, example) 
        serializer = ExampleSerializer(message)
        return Response(serializer.data)
{% endhighlight %}

## Conclusion
Permissions in Django REST Framework are a powerful way to control access to our APIs. By combining built-in permission classes with custom permissions, we can handle everything from general view-level access to fine-grained object-level control. Understanding how methods like **has_permission**, **has_object_permission**, **check_permissions**, and **check_object_permissions** work ensures that our APIs remain secure, flexible, and maintainable. We should always choose the right permission strategy based on our project’s needs to provide both safety and a smooth user experience.




