---
title: "Model Serializer - Django Rest framework"
categories: Programming
tags:
  - Python
  - Django
  - Django Rest Framework
  - Serializer

header:
  teaser: "/assets/images/2022-04-16-Model-Serializer-Django-Rest-Framework/model-serializer.png"
---

![Cover Page](/assets/images/2022-04-16-Model-Serializer-Django-Rest-Framework/model-serializer.png)
Often we will want our serializer to map closely to Django model definition. In such cases, the rest framework provides a **ModelSerializer** class which helps to automatically generate a serializer class that corresponds to the model fields.

**The model serializer has the following differences to regular serializer:**

1. On the basis of model, sets of fields are generated automatically.
2. Generates validators automatically such as **unique** and **unique together**.
3. It comes with basic **.create()** and **.update()** implementations.

**What declaring a model serializer looks like:**
{% highlight python linenos %}
class StudentSerializer(serializers.ModelSerializer):
class Meta:
model = Student
fields = ['id', 'first_name', 'last_name', 'email']

{% endhighlight %}

Now, all the fields of model **Student** gets mapped into corresponding fields as said earlier. And the foreign key relationship converts into a **PrimaryKeyRelatedField**.

**Now let's inspect ModelSerializer**

Our Model Serializer looks different than the regular serializer until we see its representation.

![model serializer representation](/assets/images/2022-04-16-Model-Serializer-Django-Rest-Framework/serializer-representation.png)

We can see that underneath the representation of **ModelSerializer** is also same as regular serializer. The advantage with ModelSerializer is that it provides shortcut to automatically build serializer, saving time and increasing efficiency.
