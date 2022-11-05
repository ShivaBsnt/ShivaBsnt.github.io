---
title: Dump and Restore the PostgreSQL Database
categories: Database
tags:
  - Postgres
  - SQL

header:
  teaser: "/assets/images/2022-11-05-Dump-And-Restore-PostgresSQL-Database/dump_and_restore.png"
---

![Cover Page](/assets/images/2022-11-05-Dump-And-Restore-PostgresSQL-Database/dump_and_restore.png)
We often want to make a copy of the populated database and connect it to our backend project. The cases generally occur when an old team member does not have a script ready to populate the database and we are a newly added member to the existing project. In such a scenario, copying a database can buy us a lot of time and we can jump straight to our code. So how do we do that? Let's begin.

## Run the command below to generate dump file:

    pg_dump -h {db_host} -U {db_user} -d {db_name} > {file_name}.sql
    pg_dump -h 127.0.0.1 -U mydbuser -d mydb > mydb.sql

It creates a **mydb.sql** file. The command will ask the password for user **mydbuser**. So we need to know the password to generate the SQL file. In case you don't remember the credentials, you can change the password for the user. 
If you are creating a dump file on server, you can use a scp command to copy the file to your local machine.

### Run the command below to copy file from server to local machine:

    scp -P <port> <user>@<publicIP or domain>:/<file_source> <destination>
    scp -P 222 ubuntu@172.1.5.18:/home/ubuntu/mydb.sql mydb.sql

Once you are done copying a dump file from the server to your local machine, Create a local database. Make sure you create the database with the exact **username** and **password**  as of the server database to avoid permission issues. You can refer to this [(How To Use PostgresSQL With Django)](https://www.shivabahadurbasnet.com.np/programming/How-To-Use-PostgresSQL-With-Django-copy/) to create a database and grant permission.


### Run the command below to restore the dumped data in local database:

    psql {local_dbname} < {dump_file}
    psql mylocaldb < mydb.sql

Great! now you should have the exact data in your local database. Happy Coding!