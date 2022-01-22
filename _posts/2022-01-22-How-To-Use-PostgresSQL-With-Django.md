---
title: "How To Use PostgresSQL With Django"
categories: Programming
tags:
  - PostgresSQL
  - Python
  - Django

header:
  teaser: "/assets/images/2022-01-22-How-To-Use-PostgresSQL-With-Django/cover-page.png"
---

![My helpful screenshot](/assets/images/2022-01-22-How-To-Use-PostgresSQL-With-Django/cover-page.png)
Django is a Python-based web framework that supports model-template-view design principles. It is free, open-source, and high-level. This framework promotes web development that is both quick and tidy. It handles many of the annoyances of web development, allowing you to concentrate on developing a web app rather than reinventing the wheel.

## Lets Create a Database and Database User

Here, I assume that you have already installed postgresSQL into your computer and know how to run it on terminal. 

![postgres_console](/assets/images/2022-01-22-How-To-Use-PostgresSQL-With-Django/postgres_console.PNG)
### Step1. Create a database
Once you see a postgres console create a databse with the command below:

       CREATE DATABASE newproject;

### Step2. Create a database user
The command above only creates a database but we need to create a database user too. So let's create a user.

      CREATE USER newprojectuser WITH PASSWORD 'password';

### Step3. Grant priviliges to user on database
We have already created a database and user for the database but we need to provide all the privileges to user on that database.

      GRANT ALL PRIVILEGES ON DATABASE newproject to newprojectuser;

## Let's Configure the Django Database Settings
![database](/assets/images/2022-01-22-How-To-Use-PostgresSQL-With-Django/database.PNG)

Open the settings file of the Django app and set the database configuration as below:

         DATABASES = {
            'default': {
            'ENGINE': 'django.db.backends.postgresql',
            'NAME': 'newproject',
            'USER': 'newprojectuser',
            'PASSWORD': 'password',
            'HOST': '127.0.0.1',
            'PORT': 5432,
         }
      }

The ***NAME*** above is the name of database and ***USER*** and ***PASSWORD*** is the credential for the user of that database.

## Migrate the database and test connection
For python to communicate with PostgresSQL, it requires **psycopg2** package else it throws an exception:

![error](/assets/images/2022-01-22-How-To-Use-PostgresSQL-With-Django/error.PNG)
### Step1. Run the migrations
So install the package and run migrations command to convert models into database table.

      pip install psycopg2
      python manage.py migrate

The migration should look like the screenshot below.

![migration screenshot](/assets/images/2022-01-22-How-To-Use-PostgresSQL-With-Django/migrations.PNG)

### Step2. Create a superuser

    python manage.py createsuperuser

After running a command provide username, email and password for super user.

![super user](/assets/images/2022-01-22-How-To-Use-PostgresSQL-With-Django/super_user.PNG)

Once you have an admin account, run the Django development server and login.

      python manage.py runserver

After running server, we should see the default index page at **http://127.0.0.1:8000/**.

![super user](/assets/images/2022-01-22-How-To-Use-PostgresSQL-With-Django/django_start_screen.PNG)

Now open the Django admin login page by appending **/admin** at end of the url and enter the credential. **http://127.0.0.1:8000/admin/**

![super user](/assets/images/2022-01-22-How-To-Use-PostgresSQL-With-Django/admin_login.PNG)

If we could access the admin interface, we have confirmed that the database has stored our account information and and we have configured PostgreSQL as the backend database for a Django project.

![super user](/assets/images/2022-01-22-How-To-Use-PostgresSQL-With-Django/admin_home.PNG)