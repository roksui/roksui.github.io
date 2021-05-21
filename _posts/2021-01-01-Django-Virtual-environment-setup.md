---
#layout: post
title:  "[Django] Setting up a virtual environment"
#date:   2021-01-01 18:14:56 +0900
#categories: jekyll update
---

# Why create a virtual environment?
It separates the modules that will be used for a project from the files on the computer. For example, for a specific project, you might need an older version of Django. Also, if virtual environment is not used, it might be hard to keep track of all the installed modules.

## For Windows:

# 1. Create a project directory

{% highlight bash %}
>> mkdir myproject
>> cd myproject
{% endhighlight %}


# 2. Create a virtual environment

If virtualenv is not installed, it should be installed as follows.
{% highlight bash %}
>> pip install virtualenv
{% endhighlight %}

It is also a good idea to keep pip updated using
{% highlight bash %}
>> python -m pip install --upgrade pip
{% endhighlight %}

Now, inside the project folder, create a virtual environment.
{% highlight bash %}
>> python -m venv myvenv
{% endhighlight %}
Here, -m is to import module, venv is the virtual environment module, and myvenv is an arbitrary name.

# 3. Activate the virtual environment
{% highlight bash %}
>> myvenv\Scripts\activate
{% endhighlight %}

To deactivate, simply enter
{% highlight bash %}
>> deactivate
{% endhighlight %}

# 4. Install modules
Install the necessary modules, in this case the latest version of Django.
{% highlight bash %}
>> (myvenv) pip install django
{% endhighlight %}

and use pip freeze to check the installed modules in the virtual environment.
{% highlight bash %}
>> (myvenv) pip freeze
{% endhighlight %}

# 5. Connecting virtual environment in PyCharm
In File - Settings - Python Interpreter - Add - Existing Interpreter - Select Scripts/python.exe