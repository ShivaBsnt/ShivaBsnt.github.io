---
title: Dynamically Modifying Fields in Django Rest Framework
categories: Serializer
tags:
  - Python
  - Django
  - DjangoRestFramework

header:
  teaser: "/assets/images/2022-12-24-DynamicFieldModelSerializer/dynamic_model_field_serializer.png"
---

![Cover Page](/assets/images/2022-12-24-DynamicFieldModelSerializer/dynamic_model_field_serializer.png)
## Dynamically Modifying Fields
Once a serializer has been initialized, the dictionary of fields that are set on the serializer may be accessed using the **.fields** attribute. Accessing and modifying this attribute allows us to dynamically modify the serializer.

Modifying the fields argument directly allows us to do interesting things such as changing the arguments on serializer fields at runtime, rather than at the point of declaring the serializer.
[(Dynamically modifying fields - offical documentation)](https://www.django-rest-framework.org/api-guide/serializers/#dynamically-modifying-fields)

Let's have a glance at the source code of **DynamicFieldModelSerializer**  from Django Rest Framework offical documentation.

{% highlight python linenos %}
class DynamicFieldsModelSerializer(serializers.ModelSerializer):
    def __init__(self, instance=None, *args, **kwargs):
        # Don't pass the 'fields' arg up to the superclass
        fields = kwargs.pop('fields', None)
        # Instantiate the superclass normally
        super(DynamicFieldsModelSerializer, self).__init__(
            instance, *args, **kwargs
        )
    if fields is not None:
        allowed = set(fields)
        existing = set(self.fields.keys())
        for field_name in existing - allowed:
            self.fields.pop(field_name)
{% endhighlight %}

**DynamicFieldsModelSerializer** inherits **ModelSerializer**, therefore it is possible to define **models** and **fields** inside class  **Meta** while using **DynamicFieldsModelSerializer**.

Let's say we have a **User** model with the fields **'username'**, **'email'**, **'first_name'**, **'last_name'**, **'password'** and **'is_active'**, then we can define our serializer class using **DynamicFieldsModelSerializer** like shown below:

{% highlight python linenos %}
class UserSerializer(DynamicFieldsModelSerializer):
    class Meta:
        model = USER
        extra_kwargs = {
            'password': {
                'write_only': True
            }
        }
        fields = ['username', 'email', 'first_name', 'last_name', 'password', 'is_active']

{% endhighlight %}

With the use of **DynamicFieldModelSerializer**, we are now able to dynamically control fields in the serializer. We can pass fields in the fields keyword argument. 
Let's suppose that we only want **'username'** and **'email'** while listing data from API. Then we can explicitly choose the fields in the serializer like **UserSerializer(fields=['username', 'email'])** while using serializer for views (or ViewSets). 

{% highlight python linenos %}
class UserViewSet(ModelViewSet):
    serializer_class = UserSerializer
    queryset = USER.objects.all()

    def list(self, request, *args, **kwargs):
        queryset = self.get_queryset()
        serializer = self.get_serializer(queryset,
                                         fields=['username', 'email'], 
                                         many=True)
        return Response(serializer.data)
{% endhighlight %}

The API return only the values for **'username'** and **'email'** thus making it pass only required data, not the whole.


![User list API result](/assets/images/2022-12-24-DynamicFieldModelSerializer/list_response.png)

The question may arise that the serializer fields can be easily choosen while defining or creating the serializer class,  so why **DynmicFieldModelSerializer**? It's true that we can choose fields but there may be need to limit  those fields based on our views and logic. All of our views using same serializer class might not need same number of fields. So in such cases, we can use DynamicFieldsModelSerializer to manipulate our serializer fields.

## What's under the hood?

Now it's time to dig little deep into **DynamicFieldModelSerializer**. When we instantiate the serializer in our views like self.get_serializer(obj, fields=['username', 'email']), then the init method in DynamicFieldModelSerializer is called. Here, **fields=['username', 'email']** is holded by kwargs in dictionary data type with key **'fields'** and value **['username', 'email']**. Now, The init method from superclass is instantiated(ModelSerializer) without fields as it has been poped out with a line of code **kwargs.pop('fields', None)**.

The **self.fields.keys()** provides all the fields defined in serializer ('username', 'email',  'first_name', 'last_name', 'password', 'is_active') and  we already have **fields=('username', 'email')**. So at last all the keys from self.fields.keys() are poped out except the fields('username', 'email'). In this way, the fields are limited or manipulated using DynamicFieldModelSerializer during runtime.

Thank you for your time!! if you have any queries then please let me know in the comment section. 😊**HAPPY CODING!!!**😊