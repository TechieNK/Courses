# DjangoProject
## Week-2
## The Django Admin
A web-based administrative backend is a standard feature of modern websites. The administrative interface, or admin for short, allows trusted site administrators to create, edit and publish content, manage site users, and perform other administrative tasks.

Django comes with a built-in admin interface. With Django’s admin you can authenticate users, display and handle forms, and validate input; all automatically. Django also provides a convenient interface to manage model data.

## Accessing the Django Admin Site
Create an admin user (superuser) to log into the admin site. To create an admin user, run the following command:
```
python manage.py createsuperuser
```
Enter your desired username and press enter:
```
Username: admin
```
Django then prompts you for your email address:
```
Email address: admin@example.com
```
The final step is to enter your password. Enter your password twice, the second time to confirm your password:
```
Password: **********
Password (again): *********
Superuser created successfully.
```
Now you have created an admin user, you’re ready to use the Django admin. Let’s start the development server and explore.

First, make sure the development server is running, then open a web browser to http://127.0.0.1:8000/admin/. Log in with the superuser account you created. At the top of the index page is the Authentication and Authorization group with two types of editable content: Groups and Users. They are provided by the authentication framework included in Django. We will look at users and groups later in the chapter.



# Not Completed
