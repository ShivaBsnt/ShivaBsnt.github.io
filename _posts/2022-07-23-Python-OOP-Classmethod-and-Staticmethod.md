---
title: Python OOP classmethods and staticmethods
categories: programming
tags:
  - Python
  - OOP

header:
  teaser: "/assets/images/2022-07-23-Python-OOP-Classmethod-and-Staticmethod/staticmethod-and-classmethod.png"
---

![Cover Page](/assets/images/2022-07-23-Python-OOP-Classmethod-and-Staticmethod/staticmethod-and-classmethod.png)

### Class methods
It is a method bound to class rather than its object and is shared among all objects.  It receives class as a implicit first argument and can modify a class state which is applied to all the instances of the class.

![Cover Page](/assets/images/2022-07-23-Python-OOP-Classmethod-and-Staticmethod/classmethods.png)


### Static methods
It is a method, much like class methods, that are bound to a class rather than its object. It does not take instance or class as the first argument. It is more like a regular method and can neither modify object state nor class state.

![Cover Page](/assets/images/2022-07-23-Python-OOP-Classmethod-and-Staticmethod/staticmethod.png)

Now, let's look at the simple example below where we have a class **Employee** which takes **first_name**, **last_name** and **salary** of the employee. The method **full_name** returns the full name of employee and the method **raised_salary** sets the new salary after rasing salary by **raise_amount**.

{% highlight python linenos %}
class Employee:
    raise_amount = 1.4

    def __init__(self, first_name, last_name, salary):
        self.first_name = first_name
        self.last_name = last_name
        self.salary = salary

    def full_name(self):
        return self.first_name + " " + self.last_name

    def raised_salary(self):
        raised_salary = self.salary * self.raise_amount
        self.salary = self.salary + raised_salary

emp1 = Employee("shiva", "basnet", 50000)
emp2 = Employee("rita", "basnet", 80000)

emp1.raised_salary()
print(emp1.raise_amount) #output 1.4
print(emp1.salary)#output  120000.0

emp2.raised_salary()
print(emp2.raise_amount)#output 1.4
print(emp2.salary)#output 192000.0

{% endhighlight %}

Now let's make use of class method here to change the **raise_amount**.

{% highlight python linenos %}
class Employee:
    raise_amount = 1.4

    def __init__(self, first_name, last_name, salary):
        self.first_name = first_name
        self.last_name = last_name
        self.salary = salary

    def full_name(self):
        return self.first_name + " " + self.last_name

    def raised_salary(self):
        raised_salary = self.salary * self.raise_amount
        self.salary = self.salary + raised_salary

    @classmethod
    def update_raise_amount(cls, amount):
        cls.raise_amount = amount

emp1 = Employee("shiva", "basnet", 50000)
emp2 = Employee("rita", "basnet", 80000)

# invoking class method to change raise amount
Employee.update_raise_amount(1.5)

emp1.raised_salary()
print(emp1.raise_amount) #output 1.5
print(emp1.salary)#output  125000.0

emp2.raised_salary()
print(emp2.raise_amount)#output 1.5
print(emp2.salary)#output 200000.0

{% endhighlight %}

Here, the **classmethod** call is implicit as defined in its definition above. Though the method **update_raise_amount** takes two arguments, we have only passed **amount**.

![Cover Page](/assets/images/2022-07-23-Python-OOP-Classmethod-and-Staticmethod/cls-method-exmp1.png)

The class method has changed the state of attribute **raise_amount** of class **Employee** which has been reflected to all of its instances. The raise_amount for all instance has been now updated to **1.5** from **1.4**.

![Cover Page](/assets/images/2022-07-23-Python-OOP-Classmethod-and-Staticmethod/cls-method-exmp2.png)

It can be also use as an alternative constructor. Let's look at the example below.

{% highlight python linenos %}
class Employee:
    raise_amount = 1.4

    def __init__(self, first_name, last_name, salary):
        self.first_name = first_name
        self.last_name = last_name
        self.salary = salary

    def full_name(self):
        return self.first_name + " " + self.last_name

    def raised_salary(self):
        raised_salary = self.salary * self.raise_amount
        self.salary = self.salary + raised_salary

    @classmethod
    def update_raise_amount(cls, amount):
        cls.raise_amount = amount

    @classmethod
    def alternative_constructor(cls, employee_detail):
        first_name, last_name, salary = \
            employee_detail.split(',')
        return cls(first_name, last_name, int(salary))


emp1_detail = "shiva, basnet, 50000"
emp2_detail = "rita, basnet, 80000"

emp1 = Employee.alternative_constructor(emp1_detail)
emp2 = Employee.alternative_constructor(emp2_detail)

Employee.update_raise_amount(1.5)

emp1.raised_salary()
print(emp1.raise_amount) #output 1.5
print(emp1.salary)#output  125000.0

emp2.raised_salary()
print(emp2.raise_amount)#output 1.5
print(emp2.salary)#output 200000.0
{% endhighlight %}

Here, the details of employee is separated by comma **","** unlike above. It needs to be parsed before creating an **Employee** object. So we have used a class method that splits the employee information and then returns **Employee** object. Therefore, it is sometime called as an alternative constructor.

![Cover Page](/assets/images/2022-07-23-Python-OOP-Classmethod-and-Staticmethod/cls-method-exmp3.png)

If we want to see a real world example, we can see a class methods inside python datetime.

[https://github.com/python/cpython/blob/main/Lib/datetime.py](https://github.com/python/cpython/blob/main/Lib/datetime.py)

![Cover Page](/assets/images/2022-07-23-Python-OOP-Classmethod-and-Staticmethod/datetime.png)

Now let's come to static method. When working with classes regular methods automatically pass the instance as the first argument and we call that **self** and class methods automatically pass the class as the first argument and we call that **cls**. The static method don't pass anything automatically neither instance or class. They behave like regular function except they are included in a classes because they have some logical connection with class.

{% highlight python linenos %}
class Employee:
    raise_amount = 1.4

    def __init__(self, first_name, last_name, salary):
        self.first_name = first_name
        self.last_name = last_name
        self.salary = salary

    def full_name(self):
        return self.first_name + " " + self.last_name

    def raised_salary(self):
        raised_salary = self.salary * self.raise_amount
        self.salary = self.salary + raised_salary

    @classmethod
    def update_raise_amount(cls, amount):
        cls.raise_amount = amount

    @classmethod
    def alternative_constructor(cls, employee_detail):
        first_name, last_name, salary = \
            employee_detail.split(',')
        return cls(first_name, last_name, int(salary))

    @staticmethod
    def is_workday(day):
        """"
        Monday:0,
        Tuesday:1,
        Wednesday:2,
        Thursday:3,
        Friday:4,
        Saturday:5,
        Sunday:6
        """
        if day.weekday() == 5 or day.weekday() == 6:
            return "NO"
        else:
            return "YES"

emp1_detail = "shiva, basnet, 50000"
emp2_detail = "rita, basnet, 80000"

emp1 = Employee.alternative_constructor(emp1_detail)
emp2 = Employee.alternative_constructor(emp2_detail)
import datetime
my_date = datetime.date(2022, 7, 24)
print(emp1.is_workday(my_date))# output YES, weekday=5
{% endhighlight %}

Above we have added a static method that takes a day and simply returns whether the given date is workday or not. It does not depend on class or instance but it makes sense to keep that method in **Employee** class. As described in definition, it has not changed the object state neither class state and has not taken any implicit arguments like in class methods.
