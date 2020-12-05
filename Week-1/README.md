# DjangoProject
## Day-1

This project is to create a fully functional website created using Django. This project is created in Ubuntu.

## Django
Django is a high-level Python Web framework that encourages rapid development and clean, pragmatic design. 
Built by experienced developers, it takes care of much of the hassle of Web development, so you can focus on writing your app without needing to reinvent the wheel. 
It’s free and open source.

* Ridiculously fast.
  * Django was designed to help developers take applications from concept to completion as quickly as possible.
* Reassuringly secure.
  * Django takes security seriously and helps developers avoid many common security mistake.
* Exceedingly scalable.
  * Some of the busiest sites on the Web leverage Django’s ability to quickly and flexibly scale.

## Why do you need a framework?
To understand what Django is actually for, we need to take a closer look at the servers. The first thing is that the server needs to know that you want it to serve you a web page.

Imagine a mailbox (port) which is monitored for incoming letters (requests). This is done by a web server. The web server reads the letter and then sends a response with a webpage. But when you want to send something, you need to have some content. And Django is something that helps you create the content.

## What happens when someone requests a website from your server?
When a request comes to a web server, it's passed to Django which tries to figure out what is actually requested. It takes a web page address first and tries to figure out what to do. This part is done by Django's urlresolver (note that a website address is called a URL – Uniform Resource Locator – so the name urlresolver makes sense). It is not very smart – it takes a list of patterns and tries to match the URL. Django checks patterns from top to bottom and if something is matched, then Django passes the request to the associated function (which is called view).

Imagine a mail carrier with a letter. She is walking down the street and checks each house number against the one on the letter. If it matches, she puts the letter there. This is how the urlresolver works!

In the view function, all the interesting things are done: we can look at a database to look for some information. Maybe the user asked to change something in the data? Like a letter saying, "Please change the description of my job." The view can check if you are allowed to do that, then update the job description for you and send back a message: "Done!" Then the view generates a response and Django can send it to the user's web browser.

## Installation
1.Django is written in Python. We need Python to do anything in Django. Let's start by installing it! We want you to install the latest version of Python 3, so if you have any earlier version, you will need to upgrade it. If you already have version 3.6 or higher you should be fine.
 ```
  $ sudo apt install python3
 ```
2.Install pip    
 ```
  $ sudo apt install python3-pip
 ```
3.Install Django
 ```
  $ pip install django
 ```
 
 ## Create a Project
Create a Django project using the following command:
 ```
  $ django-admin startproject project_name
 ```

## Run the project
In the project directory
 ```
  $ python3 manage.py runserver
 ```
 
## Creating an app
To create an app for the project
 ```
  $ python3 manage.py startapp app_name
 ```

## Configuring the app to our project
Open `settings.py` in the project folder in the project directory and add the app_name as a list item in the INSTALLED_APPS.

## URL Routing
A URL is a uniform resource locator. It contains the address that links to a resource such as an HTML page.In Django, the premise is exactly the same. 
Inside each project is a `urls.py` file in the root directory. Django stores list of URLs in this file. Whenever a user types in the URL path, this file find 
the pattern that the user typed into the browser and return a page back to the user. Django based on whatever URL was matched, then triggers the function and 
the function returns back the page that the user is looking for.

To use the function:
  ```
    from django.http import HttpResponse
  ```
and define the function, like 
  ```  
    def home(request):
      return HttpResponse("Home page")
    def contact(request):
      return HttpResponse("Contact page")
  ```
We can also make the function to return API data like a JSON response and the HTTP response.  In the above function, the function returns the string. And in the `urlspattern` we need to create a path and and call the function.
  ```
    path('',home),
    path('about/',contact)
  ```

Note: In the above example, the first path is used for the home page(base url), Django triggers the function home and the 
home function return the string `Home page`. The second path is when the user adds `about/` to the base URL, Django triggers
the function contact and the contact function return the string `Contact page`.

## Using URLs and views
We can return the template to the user using URL and views. We need to create a file named `urls.py` in the app we created. Basically, whenever some one types in the base URL, we are going to send them to this URL file and this file handles all those processing necessary. For that, we need to import the path and views and we need to add the urlspattern list.
In the `views.py` file present in the base folder, we need to add
  ```
    from django.http import HttpResponse
  ```
and define the functions which is required to return the template to the user in that file. Now, in the path of each URL in the app `urls.py` file, we need to call the necessary functions from the `views.py` to return the template to the user. Also, in the base `urls.py` file, we need to import `include` and add a path in the urlspattern to call the app `urls.py` file.
  ```
    path('',include('accounts.urls'))
  ```
In the above case, `accounts` is the name of the app we created.

# DjangoProject
## Day-3 

## Templates
A template is a text file. It can generate any text-based format (HTML, XML, CSV, etc.).
A template contains variables, which get replaced with values when the template is evaluated, and tags, which control the logic of the template.

Our templates needs to be stored in app_folder-> templates-> app_name-> template_name.html. To display that template using function we can use `render()` method. 
  ```
  def home(request):
    return render(request, 'accounts/dashboard.html')
  ```
Here, `accounts` is the name of the directory in templates directory where templates are present and `dashboard.html` is our template . 

### Tags
Tags look like this: `{% tag %}`. Tags are more complex than variables: Some create text in the output, some control flow by performing loops or logic, and some load external information into the template to be used by later variables. Some tags require beginning and ending tags (i.e. `{% tag %} ... tag contents ... {% endtag %}`).
Django ships with about two dozen built-in template tags.

When lot of templates needs to used, we could get a lot of redundant code. So, to solve this issue, we use the concept of template inheritance. 

## Template inheritance
The most powerful – and thus the most complex – part of Django’s template engine is template inheritance. Template inheritance allows you to build a base template that contains all the common elements of your site and defines blocks that child templates can override. For that a section need to be added in the base template.
  ```
    {% block content %}
    
    
    {% endblock %}
  ```
Now, the child template can reside in this section. In order to use the base template, the child template need to extend the base template.
  ```
  {% extends 'path_of_base_template' %}
  ```
Now, we need to specify the section and add the content in the section. 

**Here are some tips for working with inheritance:**

1. If you use {% extends %} in a template, it must be the first template tag in that template. Template inheritance won’t work, otherwise.

2. More {% block %} tags in your base templates are better. Remember, child templates don’t have to define all parent blocks, so you can fill in reasonable defaults in a number of blocks, then only define the ones you need later.

3. If you find yourself duplicating content in a number of templates, it probably means you should move that content to a {% block %} in a parent template.

4. If you need to get the content of the block from the parent template, the {{ block.super }} variable will do the trick. This is useful if you want to add to the contents of a parent block instead of completely overriding it. Data inserted using {{ block.super }} will not be automatically escaped, since it was already escaped, if necessary, in the parent template.

5. By using the same template name as you are inheriting from, {% extends %} can be used to inherit a template at the same time as overriding it. Combined with {{ block.super }}, this can be a powerful way to make small customizations.

We can also have our header, footer in the  differents template. To have header, footer in a different
templates, create new templates, add the code and include it in the base template.
  ```
  {% include 'path_of_template' %}
  ```
We can also have our css and javascript code in the base template and only html code in the other template(header, footer, etc...). 

