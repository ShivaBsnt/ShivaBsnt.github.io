---
title: "Validation in Django REST Framework Serializers"
categories: "DRF"
tags:
  - "Serializer"
  - "Python"
  - "Django"
  - "Django REST Framework"
  - "Validation"
  - "Field Level Validation"
  - "Object Level Validation"
  - "Validators"

header:
  teaser: "/assets/images/2025-10-04-Validation-in-Django-REST-Framework-Serializers/teaser.png"
---
![Validation in django restframework cover page](/assets/images/2025-10-04-Validation-in-Django-REST-Framework-Serializers/cover.png)

Validation is a core part of any REST API. It ensures that the data received from clients is correct, consistent, and secure before it reaches our database.

In Django REST Framework (DRF), serializers handle both data transformation and validation.
When we call serializer.is_valid(), DRF runs several layers of validation behind the scenes — from simple field checks to complex, multi-field rules.

In this article, we’ll look at the three main types of validation we can perform directly inside a serializer.

## 1. Field-Level Validation
Field-level validation focuses on validating a **single field** at a time.
    
We can specify custom field-level validation by adding **.validate_<field_name\>** methods to our **Serializer** subclass.

```Python
    validate_<field_name>(self, value)
```
    
This method takes a single argument, which is the field value that requires validation.

Our **validate_<field_name\>** methods should return the validated value or raise a **serializers.ValidationError**.

#### Example:
```Python
    from rest_framework import serializers

    class UserSerializer(serializers.Serializer):
        username = serializers.CharField(max_length=50)
        age = serializers.IntegerField()

        def validate_age(self, value):
            if value < 18:
                raise serializers.ValidationError("User must be at least 18 years old.")
            return value 
```
> [! TIP]
> Use field-level validation for single-field checks such as range limits, allowed characters, or forbidden values.

> [! NOTE]
> If your **<field_name\>** is declared on your serializer with the parameter **required=False**, then this validation step will not take place if the field is not included.

## 2. Object-Level Validation
Sometimes, validation depends on multiple fields - for instance, ensuring one date comes after another or one amount is greater than another.

In such cases, define a method named **.validate(self, attrs)** inside our serializer.

```Python
validate(self, attrs)
```
This method takes a single argument, which is all the validated field values as a dictionary.

It should raise a **serializers.ValidationError** if necessary, or just return the validated values.

#### Example:
```Python
class BookingSerializer(serializers.Serializer):
    start_date = serializers.DateField()
    end_date = serializers.DateField()

    def validate(self, attrs):
        if attrs['start_date'] > attrs['end_date']:
            raise serializers.ValidationError("End date must be after start date.")
        return attrs
```
> [! TIP]
> Use object-level validation for cross-field logic where multiple values need to be compared or related.

## 3. Validators
We can also attach external validator functions directly to serializer fields using the validators argument.

This approach is ideal when the same validation rule is used across multiple serializers.

```Python
def validate_even(value):
    if value % 2 != 0:
        raise serializers.ValidationError("This field must be an even number.")

class NumberSerializer(serializers.Serializer):
    number = serializers.IntegerField(validators=[validate_even])
```
> [! TIP]
> Use validator functions to keep our code modular and clean - perfect for shared validation logic.

Serializer classes can also include reusable validators that are applied to the complete set of field data. These validators are included by declaring them on an inner Meta class, like so:
```Python
from rest_framework import serializers
from rest_framework.validators import UniqueTogetherValidator
from myapp.models import UserProfile

class UserProfileSerializer(serializers.ModelSerializer):
    class Meta:
        model = UserProfile
        fields = ['username', 'email']
        validators = [
            # no two users have the same combination of username and email
            UniqueTogetherValidator(
                queryset=UserProfile.objects.all(),
                fields=['username', 'email']
            )
        ]
```
## 📋 Validation Flow in a Serializer
When .is_valid() is called, DRF executes validation in this order:
### 1. Built-in Field Validation
Each field (like CharField, EmailField, etc.) first runs its native Django/DRF validation, such as:

- required

- max_length

- min_value

- choices

- allow_blank, etc.

These are handled by the field’s own run_validation() and run_validators()methods.

### 2. Field-Level Validators (attached via validators=[...])
After built-in validation, DRF executes explicit validators attached to each field.
These are functions or validator classes passed like this:
```Python
number = serializers.IntegerField(validators=[validate_even])
```
### 3. Custom Field-Level Validation (validate_<fieldname\>)
After built-in and field-attached validators, DRF calls our serializer’s validate_<field_name\> methods.
```Python
def validate_age(self, value):

```
These methods are defined inside our serializer and handle logic specific to one field.

### 4. Object-Level Validation (validate(self, attrs))
Once all fields have been individually validated, DRF passes the complete dictionary of validated data (attrs) to our serializer’s validate() method.
This is where we can check inter-field relationships.

### 5. Meta Validators (Meta.validators)
Finally, DRF runs any validators listed inside the serializer’s Meta class.
This includes both:
- Built-in validators like UniqueTogetherValidator
- Custom validator functions that work at the object level

## 🚀 Summary
<table>
  <thead>
    <tr>
      <th>Type</th>
      <th>Method</th>
      <th>Validates</th>
      <th>Use Case</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Field-Level</td>
      <td><code>validate_<field_name></code></td>
      <td>One field</td>
      <td>Simple field-based rules</td>
    </tr>
    <tr>
      <td>Object-Level</td>
      <td><code>validate(self, attrs)</code></td>
      <td>Multiple fields</td>
      <td>Dependent or relational checks</td>
    </tr>
    <tr>
      <td>Validators</td>
      <td><code>validators=[...]</code></td>
      <td>Field or Object</td>
      <td>Reusable standalone functions</td>
    </tr>
  </tbody>
</table>


## ✨ Conclusion

Serializer validation is one of the most powerful features in Django REST Framework.
By combining field-level, object-level, and reusable validators, we can write clean, reliable, and secure API logic — keeping our data trustworthy at every level.

