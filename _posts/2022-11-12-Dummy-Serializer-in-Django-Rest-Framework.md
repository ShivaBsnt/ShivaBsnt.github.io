---
title: Dummy Serializer in Django Rest Framework
categories: Web
tags:
  - Python
  - Django
  - Django Rest Framework

header:
  teaser: "/assets/images/2022-11-12-Dummy-Serializer-in-Django-Rest-Framework/dummyserializer.png"
---

![Cover Page](/assets/images/2022-11-12-Dummy-Serializer-in-Django-Rest-Framework/dummyserializer.png)
 Dummy serializer is the serializer used in viewset having no actual implementation or use case.

For example we have a viewset called **ContentViewSet**. This viewset gives user access to content such as **article**, **quiz**, **video** etc. Now, we have a action called **user_content_log** which stores the log of user on our database. This logs can be really helpful in identifying user recent watched or viewed content.

### User Content Log Action under ContentViewSet
{% highlight python linenos %}
@action(detail=True, methods=['post'], url_path='user-content-log')
def user_content_log(self, *args, **kwargs):
    content = self.get_object()
    user = self.request.user
    from datetime import datetime
    UserContentLog.objects.update_or_create(user=user,
      content=content, defaults={'updated_at': datetime.now()})
    return Response({
        'Video log created!'
    }, status=status.HTTP_200_OK)

{% endhighlight %}

The above code shows the action to store user content log. The **UserContentLog** model requires only two fields **content** and **user**. Since the action is written under **ContentViewSet** and the **detail** field is set to **True**, we can get the content object from **self.get_object()** and the user field is available in **request.user**. In such a case, we do not require a serializer for validation as both fields are available to backend.

### ContentViewSet Serializer Class
{% highlight python linenos %}

def get_serializer_class(self):
    return ContentSerializer

{% endhighlight %}

Now, though we do not require a serializer class for action, we require a serializer class for viewset. If we do not provide a serializer class for **user_content_log** action, **ContentViewSet** serializer class which is **ContentSerializer** will be used by default. 

### Is it really a big problem?
The short answer is **No**. It does not make a big difference. Neither will this raise an error because the serializer class is never called inside our **user_content_log** action. But this will prevents us from wrong swagger docs. Since the **user_content_log** action is using a wrong serializer class, the swagger docs will show false information.

## So what can we do?
We can make a dummy serializer class like shown below: 

{% highlight python linenos %}
class DummySerializer(serializers.Serializer):
    # dummy serializer for using when serializer class 
    # is needed in viewset but actual serializer is not used
    # for maintaining swagger doc
    pass

{% endhighlight %}

And we can override the get_serializer_class of **ContentViewSet** and provide **DummySerializer** to **user_content_log** action.


{% highlight python linenos %}
def get_serializer_class(self):
    if self.action == 'user_content_log':
        return DummySerializer
    return ContentSerializer

{% endhighlight %}

Bravo! we are done. Now the swagger docs does not show false information as the serializer for **user_content_log** action is **DummySerializer**.
