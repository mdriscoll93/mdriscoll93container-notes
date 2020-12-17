# Django 

 ---

 ## Basic Django Project Directory Contents

 > [!TIP]
> The following commands and subsequent directory structure definitions represent a barebones instance of a Django Project
>> [!NOTE] 
>> the Python3 command is used to create a SQLlite database instance rather than one of the full featured databases supported by Django Version 3.0+

### Step 1. Creating an isolated Python Environment

```Bash
## Initialize test venv

$ Python3 -m venv my_env

## Creates directory structure "my_env/lib/python3.8/site-packages"

$ source my_env/bin/activate
$ (my_env) pip install "Django==3.0.*"
$ (my_env) Python3
>>> import django
>>> django.get_version()
'3.0.4'
```
### Step 2. Create first project within venv

```Bash
$ (my_env) django-admin startproject mysite
$ (my_env) tree

  mysite/
    manage.py
    mysite/
      __init__.py
      asgi.py
      wsgi.py
      settings.py
      urls.py
```


| dir/file        | summary                                                                                                                            |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------|
| manage.py       | This is a command-line utility used to interact with your project. It is a thin wrapper around the django-admin.py tool            |
| django-admin.py |                                                                                                                                    |
| mysite/         | This is your project directory, which consists of the following files:                                                             |
| __init__.py     | An empty file that tells Python to treat the mysite directory as a Python module.                                                  |
| asgi.py         | This is the configuration to run your project as ASGI, the emerging Python standard for asynchronous web servers and applications. |
| settings.py     | This indicates settings and configuration for your project and contains initial default settings.                                  |
| urls.py         | This is the place where your URL patterns live. Each URL defined here is mapped to a view.                                         |
| wsgi.py         | This is the configuration to run your project as a Web Server Gateway Interface (WSGI) application.                                |
| db.sqlite3      | Lightweight database created by using Python3 natively against Django                                                              |
