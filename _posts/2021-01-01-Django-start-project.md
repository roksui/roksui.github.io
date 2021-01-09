---
layout: post
title:  "[Django] Starting a project"
date:   2021-01-01 18:48:56 +0900
categories: jekyll update
---

## For Windows:

# 1. Starting up a new project

Inside the project directory(folder),
{% highlight bash %}
>> (myvenv) django-admin startproject myproject
{% endhighlight %}

# 2. Try running the project

After cd-ing into the myproject folder, run the local server.
{% highlight bash %}
>> (myvenv) python manage.py runserver
{% endhighlight %}

# 3. Database Setting (Migrations)

Django's default database is SQLite. Migrations are Django's method of making changes made to the models into the database.
{% highlight bash %}
>> (myvenv) python manage.py makemigrations
>> (myvenv) python manage.py migrate
{% endhighlight %}
'makemigrations' command creates the migrations (generates the SQL commands)

'migrate' command applies igrations. (executes the SQL commands)

# 4. Create Superuser (admin)
{% highlight bash %}
>> (myvenv) python manage.py createsuperuser
{% endhighlight %}

# 5. Create an app
If you want to add a functionality to your website, an app does it.
{% highlight bash %}
>> (myvenv) python manage.py startapp survey
{% endhighlight %}

In settings.py - INSTALLED_APPS, add the new app's name.