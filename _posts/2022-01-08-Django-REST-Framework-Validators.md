---
title: "Django REST Framework Validators"
categories: Programming
tags:
  - Python
  - Django
  - Django REST Framework

header:
  teaser: "/assets/images/2022-01-08-Django-REST-Framework-Validators/rest_framework_validators.png"
---

![My helpful screenshot](/assets/images/2022-01-08-Django-REST-Framework-Validators/rest_framework_validators.png)
Validation in REST framework is entirely performed on serializer class. There are no any hidden behavior on model instance. All of the validation rule can be exactly seen by printing the representation of serializer.

#### Lets make the model for customer report record

{% highlight python linenos %}
from django.utils import timezone
from django.db import models

class CustomerReportRecord(models.Model):
time_raised = models.DateTimeField(default=timezone.now, editable=False)
reference = models.CharField(unique=True, max_length=20)
description = models.TextField()
{% endhighlight %}

#### Now let's make the model serializer for validation

{% highlight python linenos %}
from rest_framework import serializers
from .models import CustomerReportRecord

class CustomerReportSerializer(serializers.ModelSerializer):
class Meta:
model = CustomerReportRecord
fields = "**all**"

{% endhighlight %}

#### Representation of CustomerReportSerializer

{% highlight python linenos %}

> > > from app.serializers import CustomerReportSerializer
> > > CustomerReportSerializer()
> > > CustomerReportSerializer():

    id = IntegerField(label='ID', read_only=True)
    time_raised = DateTimeField(read_only=True)
    reference = CharField(max_length=20, validators=[<UniqueValidator(queryset=CustomerReportRecord.objects.all())>])
    description = CharField(style={'base_template': 'textarea.html'})

{% endhighlight %}
The **_unique = true_** validation of reference field in model has been imposed by a validators. The validators used is **_Unique Validator_** derived from REST Framework.We can impose same validation without defining validation on model.

#### Lets make model again without any validation

{% highlight python linenos %}
from django.utils import timezone
from django.db import models

class CustomerReportRecord(models.Model):
time_raised = models.DateTimeField(default=timezone.now, editable=False)
reference = models.CharField(max_length=20)
description = models.TextField()

{% endhighlight %}

Now there is no any validation written on model. But we will get same behaviour and representation by over riding the serializer.

{% highlight python linenos %}
from rest_framework import serializers
from rest_framework.validators import UniqueValidator
from .models import CustomerReportRecord

class CustomerReportSerializer(serializers.ModelSerializer):
reference = serializers.CharField(max_length=20, validators=[UniqueValidator(queryset=CustomerReportRecord.objects.all())])

    class Meta:
        model = CustomerReportRecord
        fields = "__all__"

{% endhighlight %}
Here the definition of **_reference_** in model has been changed by over riding the reference field. The **_UniqueValidator_** is imported from the REST framework and if we look at the representation of the serializer, it gives the same result.

{% highlight python linenos %}

> > > from app.serializers import CustomerReportSerializer
> > > CustomerReportSerializer()
> > > CustomerReportSerializer():

    id = IntegerField(label='ID', read_only=True)
    reference = CharField(max_length=20, validators=[<UniqueValidator(queryset=<QuerySet []>)>])
    time_raised = DateTimeField(read_only=True)
    description = CharField(style={'base_template': 'textarea.html'})

{% endhighlight %}
On top of that, custom message can be set on validation error. The unique validator accepts **_message_** field which takes string and is raised on validation error instead of a default message which is **_"This field must be unique."_**

Every validation can be written without using model let's impose unique together only using serializer.Normally we write **_unique_together = True_** under the meta class of model to achieve unique together validation.

{% highlight python linenos %}
from django.utils import timezone
from django.db import models


class CustomerReportRecord(models.Model):
    time_raised = models.DateTimeField(default=timezone.now, editable=False)
    reference = models.CharField(max_length=20)
    description = models.TextField()

    class Meta:
        unique_together = ['reference', 'description']

{% endhighlight %}

{% highlight python linenos %}
>>> from app.serializers import CustomerReportSerializer
>>> CustomerReportSerializer()
CustomerReportSerializer():
    id = IntegerField(label='ID', read_only=True)
    reference = CharField(max_length=20, validators=[<UniqueValidator(queryset=<QuerySet []>)>])
    time_raised = DateTimeField(read_only=True)
    description = CharField(required=True, style={'base_template': 'textarea.html'})
    class Meta:
        validators = [<UniqueTogetherValidator(queryset=CustomerReportRecord.objects.all(), fields=('reference', 'description'))>]

{% endhighlight %}

#### Now lets use only serializer to obtain unique together validation

{% highlight python linenos %}
from rest_framework import serializers
from rest_framework.validators import UniqueValidator, UniqueTogetherValidator
from .models import CustomerReportRecord


class CustomerReportSerializer(serializers.ModelSerializer):
    reference = serializers.CharField(max_length=20, validators=[UniqueValidator(queryset=CustomerReportRecord.objects.all())])

    class Meta:
        model = CustomerReportRecord
        fields = "__all__"
        validators = [UniqueTogetherValidator(queryset=CustomerReportRecord.objects.all(), fields=('reference',
                                                                                                   'description'))]

{% endhighlight %}

{% highlight python linenos %}
>>> from app.serializers import CustomerReportSerializer
>>> CustomerReportSerializer()
CustomerReportSerializer():
    id = IntegerField(label='ID', read_only=True)
    reference = CharField(max_length=20, validators=[<UniqueValidator(queryset=<QuerySet []>)>])
    time_raised = DateTimeField(read_only=True)
    description = CharField(style={'base_template': 'textarea.html'})
    class Meta:
        validators = [<UniqueTogetherValidator(queryset=<QuerySet []>, fields=('reference', 'description'))>]

{% endhighlight %}

{% highlight python linenos %}
from django.utils import timezone
from django.db import models


class CustomerReportRecord(models.Model):
    time_raised = models.DateTimeField(default=timezone.now, editable=False)
    reference = models.CharField(max_length=20)
    description = models.TextField()
{% endhighlight %}