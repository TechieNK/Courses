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

First, make sure the development server is running, then open a web browser to http://127.0.0.1:8000/admin/. Log in with the superuser account you created. At the top of the index page is the Authentication and Authorization group with two types of editable content: Groups and Users. They are provided by the authentication framework included in Django. 

## Models
A model is the single, definitive source of information about your data. It contains the essential fields and behaviors of the data you’re storing. It is a class that represents table or collection in our DB, and where every attribute of the class is a field of the table or collection.  In short, Django Models is the SQL of Database one uses with Django. SQL (Structured Query Language) is complex and involves a lot of different queries for creating, deleting, updating or any other stuff related to database. Django models simplify the tasks and organize tables into models. Models are defined in the app/models.py

The basics:

* Each model is a Python class that subclasses **django.db.models.Model**.
* Each attribute of the model represents a database field.
* With all of this, Django gives you an automatically-generated database-access API:

## Writing models in Python has several advantages:

* **Simplicity**: Writing Python is not only easier than writing SQL, but it’s less error-prone and more efficient as your brain doesn’t have to keep switching from one language to another.
* **Consistency**: As I mentioned earlier, SQL is inconsistent across different databases. You want to describe your data once, not create separate sets of SQL statements for each database to which the application will be deployed.
* **Avoids Introspection**: Any application has to know something about the underlying database structure. There are only two ways to do that: have an explicit description of the data in the application code, or introspect the database at runtime. As introspection has a high overhead and is not perfect, Django’s creators chose the first option.
* **Version Control**: Storing models in your codebase makes it easier to keep track of design changes.
* **Advanced Metadata**: Having models described in code rather than SQL allows for special data-types (e.g., email addresses), and provides the ability to store much more metadata than SQL.

A drawback is the database can get out of sync with your models, but Django takes care of this problem with migrations.

## Quick example 
This example model defines a **Person**, which has a **first_name** and **last_name**:
```
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```
**first_name** and **last_name** are fields of the model. Each field is specified as a class attribute, and each attribute maps to a database column.

The above **Person** model would create a database table like this:
```
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```
## Fields
A model can have an arbitrary number of fields, of any type — each one represents a column of data that we want to store in one of our database tables. Each database record (row) will consist of one of each field value. Let's look at the example seen below:
```
first_name = models.CharField(max_length=20, help_text='Enter field documentation')
```
Our above example has a single field called **first_name**, of type models.CharField — which means that this field will contain strings of alphanumeric characters. The field types are assigned using specific classes, which determine the type of record that is used to store the data in the database, along with validation criteria to be used when values are received from an HTML form (i.e. what constitutes a valid value). The field types can also take arguments that further specify how the field is stored or can be used. In this case we are giving our field two arguments:

* `max_length=20` — States that the maximum length of a value in this field is 20 characters.
* `help_text='Enter field documentation'` — provides a text label to display to help users know what value to provide when this value is to be entered by a user via an HTML form.

The field name is used to refer to it in queries and templates. Fields also have a label specified as an argument (`verbose_name`), the default value of which is `None`, meaning replacing any underscores in the field name with a space (for example first_name would have a default label of first name). Note that when the label is used as a form label through Django frame, the first letter of the label is capitalised (for example first_name would be First name).

The order that fields are declared will affect their default order if a model is rendered in a form (e.g. in the Admin site), though this may be overridden.

## Common field arguments
The following common arguments can be used when declaring many/most of the different field types:

* **help_text**: Provides a text label for HTML forms (e.g. in the admin site), as described above.
* **verbose_name**: A human-readable name for the field used in field labels. If not specified, Django will infer the default verbose name from the field name.
* **default**: The default value for the field. This can be a value or a callable object, in which case the object will be called every time a new record is created.
* **null**: If `True`, Django will store blank values as `NULL` in the database for fields where this is appropriate (a `CharField` will instead store an empty string). The default is `False`.
* **blank**: If `True`, the field is allowed to be blank in your forms. The default is `False`, which means that Django's form validation will force you to enter a value. This is often used with `null=True` , because if you're going to allow blank values, you also want the database to be able to represent them appropriately.
* **choices**: A group of choices for this field. If this is provided, the default corresponding form widget will be a select box with these choices instead of the standard text field.
* **primary_key**: If `True`, sets the current field as the primary key for the model (A primary key is a special database column designated to uniquely identify all the different table records). If no field is specified as the primary key then Django will automatically add a field for this purpose.

There are many other options — you can view the <a href="https://docs.djangoproject.com/en/3.1/ref/models/fields/#field-options">full list of field options here</a>.

## Common field types
The following list describes some of the more commonly used types of fields. 

* **CharField** is used to define short-to-mid sized fixed-length strings. You must specify the `max_length of the data to be stored.
* **TextField** is used for large arbitrary-length strings. You may specify a `max_length` for the field, but this is used only when the field is displayed in forms (it is not enforced at the database level).
* **IntegerField** is a field for storing integer (whole number) values, and for validating entered values as integers in forms.
* **DateField** and **DateTimeField** are used for storing/representing dates and date/time information (as Python `datetime.date` in and `datetime.datetime` objects, respectively). These fields can additionally declare the (mutually exclusive) parameters `auto_now=True` (to set the field to the current date every time the model is saved), `auto_now_add` (to only set the date when the model is first created) , and `default` (to set a default date that can be overridden by the user).
* **EmailField** is used to store and validate email addresses.
* **FileField** and **ImageField** are used to upload files and images respectively (the `ImageField` simply adds additional validation that the uploaded file is an image). These have parameters to define how and where the uploaded files are stored.
* **AutoField** is a special type of `IntegerField` that automatically increments. A primary key of this type is automatically added to your model if you don’t explicitly specify one.
* **ForeignKey** is used to specify a one-to-many relationship to another database model (e.g. a car has one manufacturer, but a manufacturer can make many cars). The "one" side of the relationship is the model that contains the "key" (models containing a "foreign key" referring to that "key", are on the "many" side of such a relationship).
* **ManyToManyField** is used to specify a many-to-many relationship (e.g. a book can have several genres, and each genre can contain several books). In our library app we will use these very similarly to `ForeignKeys`, but they can be used in more complicated ways to describe the relationships between groups. These have the parameter `on_delete` to define what happens when the associated record is deleted (e.g. a value of `models.SET_NULL` would simply set the value to `NULL`).

There are many other types of fields, including fields for different types of numbers (big integers, small integers, floats), booleans, URLs, slugs, unique ids, and other "time-related" information (duration, time, etc.). You can view the <a href="https://docs.djangoproject.com/en/3.1/ref/models/fields/#field-types">full list here</a>.

## Migrations
Migrations are Django’s way of propagating changes you make to your models (adding a field, deleting a model, etc.) into your database schema. They’re designed to be mostly automatic, but you’ll need to know when to make migrations, when to run them, and the common problems you might run into.

## The Commands
There are several commands which you will use to interact with migrations and Django’s handling of database schema:

* **migrate**, which is responsible for applying and unapplying migrations.
* **makemigrations**, which is responsible for creating new migrations based on the changes you have made to your models.
* **sqlmigrate**, which displays the SQL statements for a migration.
* **showmigrations**, which lists a project’s migrations and their status.

You should think of migrations as a version control system for your database schema. **makemigrations** is responsible for packaging up your model changes into individual migration files - analogous to commits - and **migrate** is responsible for applying those to your database.

The migration files for each app live in a “migrations” directory inside of that app, and are designed to be committed to, and distributed as part of, its codebase. You should be making them once on your development machine and then running the same migrations on your colleagues’ machines, your staging machines, and eventually your production machines.

**Note**:
It is possible to override the name of the package which contains the migrations on a per-app basis by modifying the **MIGRATION_MODULES** setting.

## Registering the model
Usually, these are stored in a file named admin.py in your application. 
```
from django.contrib import admin
from .models import Author

admin.site.register(Author)
```
This examples registers the `Author` model. Now, we can see the Author model in the `admin panel`.

## Relationships in Django models
Django models operate by default on relational database systems (RDBMS) and thus they also support relationships amongst one another. In the simplest terms, database relationships are used to associate records on the basis of a key or id, resulting in improved data maintenance, query performance and less duplicate data, among other things.

Django models support the same three relationships supported by relational database systems: One to many, many to many and one to one.

## One to many relationships in Django models
A one to many relationship implies that one model record can have many other model records associated with itself. For example, a `Menu` model record can have many `Item` model records associated with it and yet an `Item` belongs to a single `Menu` record. To define a one to many relationship in Django models you use the `ForeignKey` data type on the model that has the many records (e.g. on the `Item` model).

```
from django.db import models

class Menu(models.Model):
    name = models.CharField(max_length=30)

class Item(models.Model):
    menu = models.ForeignKey(Menu)
    name = models.CharField(max_length=30)
    description = models.CharField(max_length=100)
```
The first Django model in the above example is `Menu` and has the `name` field (e.g. `Menu` instances can be `Breakfast`, `Lunch`, `Drinks`,etc). Next, in the above example is the `Item` Django model which has a `menu` field, that itself has the `models.ForeignKey(Menu)` definition. The `models.ForeignKey()` definition creates the one to many relationship, where the first argument Menu indicates the relationship model.

## Many to many relationships in Django models
A many to many relationship implies that many records can have many other records associated amongst one another. For example, `Store` model records can have many `Amenity` records, just as `Amenity` records can belong to many `Store` records. To define a many to many relationship in Django models you use the `ManyToManyField` data type.
```
from django.db import models

class Amenity(models.Model):
    name = models.CharField(max_length=30)
    description = models.CharField(max_length=100)

class Store(models.Model):
    name = models.CharField(max_length=30)    
    address = models.CharField(max_length=30)
    city = models.CharField(max_length=30)
    state = models.CharField(max_length=2)
    email = models.EmailField()
    amenities = models.ManyToManyField(Amenity,blank=True)
```
The first Django model in the above example is `Amenity` and has the `name` and `description` fields. Next, in the above example is the `Store` Django model which has the `amenities` field, that itself has the `models.ManyToManyField(Amenity,blank=True)` definition. The `models.ManyToManyField()` definition creates the many to many relationship via a junction table, where the first argument `Amenity` indicates the relationship model and the optional `blank=True` argument allows a `Store` record to be created without the need of an `amenities` value.

In this case, the junction table created by Django is used to hold the relationships between the `Amenity` and `Store` records through their respective keys. Although you don't need to manipulate the junction table directly, for reference purposes, Django uses the syntax `<model_name>_<model_field_with_ManyToManyField>` to name it (e.g. For `Store` model records stored in the `stores_store` table and `Amenity` model records stored in the `stores_amenity` table, the junction table is `stores_store_amenities`)

## One to one relationships in Django models
A one to one relationship implies that one record is associated with another record. If you're familiar with object-orientated programming, a one to one relationship in RDBMS is similar to object-oriented inheritance that uses the is a rule (e.g. a Car object is a Vehicle object).

For example, generic `Item` model records can have a one to one relationship to `Drink` model records, where the latter records hold information specific to drinks (e.g. caffeine content) and the former records hold generic information about items (e.g. price). To define a one to one relationship in Django models you use the `OneToOneField` data type.
```
from django.db import models

class Menu(models.Model):
    name = models.CharField(max_length=30)

class Item(models.Model):
    menu = models.ForeignKey(Menu)
    name = models.CharField(max_length=30)
    description = models.CharField(max_length=100)
    calories = models.IntegerField()
    price = models.FloatField()

class Drink(models.Model):
    item = models.OneToOneField(Item,on_delete=models.CASCADE,primary_key=True)
    caffeine = models.IntegerField()
```
The first Django model in the above example is `Item` which is similar to the one presented in one to many relationship example, except the version in the above example has the additional `calories` and `price` fields. Next, in the above example is the `Drink` model which has the `item` field, that itself has the `models.OneToOneField(Amenity,on_delete=models.CASCADE,primary_key=True)` definition.

The `models.OneToOneField()` definition creates the one to one relationship, where the first argument `Item` indicates the relationship model. The second argument `on_delete=models.CASCADE` tells Django that in case the relationship record is deleted (i.e. the `Item`) its other record (i.e. the `Drink`) also be deleted, this last argument prevents orphaned data. Finally, the `primary_key=True` tells Django to use the relationship id (i.e. `Drink.id`) as the primary key instead of using a separate and default column `id`, a technique that makes it easier to track relationships.

## Data integrity options: on_delete
All model relationships create dependencies between one another, so an important behavior to define is what happens to the other party when one party is removed. The **on_delete** option is designed for this purpose, to determine what to do with records on the other side of a relationship when one side is removed.

For example, if an `Item` model has a `menu ForeignKey()` field pointing to a `Menu` model (i.e. a one to many relationship: an `Item` always belong to one `Menu`, and a `Menu` has many `Items`), what happens to `Item` model records if their related `Menu` model instance is deleted ? Are the `Item` model records also deleted ?

The **on_delete** option is available for all three relationship model data types and supports the following values:

* **on_delete=models.CASCADE (Default)**- Automatically deletes related records when the related instance is removed (e.g. if the `Menu` Breakfast instance is deleted, all `Item` records referencing the `Menu` Breakfast instance are also deleted)
* **on_delete=models.PROTECT**- Prevents a related instance from being removed (e.g. if the `menu` field on `Item` uses `ForeignKey(Menu,on_delete=models.PROTECT)`, any attempt to remove `Menu` instances referenced by `Item` instances are blocked).
* **on_delete=models.SET_NULL**- Assigns NULL to related records when the related instance is removed, note this requires the field to also use the `null=True` option (e.g. if the `Menu` Breakfast instance is deleted, all `Item` records referencing the `Menu` Breakfast instance are assigned NULL to their `menu` field value).
* **on_delete=models.SET_DEFAULT**- Assigns a default value to related records when the related instance is removed, note this requires the field to also use a `default` option value (e.g. if the `Menu` Breakfast instance is deleted, all `Item` records referencing the `Menu` Breakfast instance are assigned a default `Menu` instance to their `menu` field value).
* **on_delete=models.SET**- Assigns a value set through a callable to related records when the related instance is removed (e.g. if the `Menu` Breakfast instance is deleted, all `Item` records referencing the `Menu` Breakfast instance are assigned an instance to their `menu` field value set through a callable function).
* **on_delete=models.DO_NOTHING**- No action is taken when related records are removed. This is generally a bad relational database practice, so by default, databases will generate an error since you're leaving orphaned records with no value, null or otherwise. If you use this value, you must ensure the database table does not enforce referential integrity.
