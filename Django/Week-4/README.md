# DjangoProject
## Week-4

## Dynamic URL Routing & Templates
A clean, elegant URL scheme is an important detail in a high-quality Web application. Django lets you design URLs however you want, with no framework limitations.
```
urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/', views.article_detail),
]
```
**Notes**:

* To capture a value from the URL, use angle brackets.
* Captured values can optionally include a converter type. For example, use **<int:name>** to capture an integer parameter. If a converter isn’t included, any string, excluding a / character, is matched.
* There’s no need to add a leading slash, because every URL has that. For example, it’s **articles**, not **/articles**.

## Example requests:

* A request to **/articles/2005/03/** would match the third entry in the list. Django would call the function **views.month_archive(request, year=2005, month=3)**.
* **/articles/2003/** would match the first pattern in the list, not the second one, because the patterns are tested in order, and the first one is the first test to pass. Here, Django would call the function **views.special_case_2003(request)**.
* **/articles/2003** would not match any of these patterns, because each pattern requires that the URL end with a slash.
* **/articles/2003/03/building-a-django-site/** would match the final pattern. Django would call the function **views.article_detail(request, year=2003, month=3, slug="building-a-django-site")**.

## Path converters
The following path converters are available by default:

* **str** - Matches any non-empty string, excluding the path separator, '/'. This is the default if a converter isn’t included in the expression.
* **int** - Matches zero or any positive integer. Returns an **int**.
* **slug** - Matches any slug string consisting of ASCII letters or numbers, plus the hyphen and underscore characters. For example, **building-your-1st-django-site**.
* **uuid** - Matches a formatted UUID. To prevent multiple URLs from mapping to the same page, dashes must be included and letters must be lowercase. For example, **075194d3-6885-417e-a8a8-6c931e272f00**. Returns a **UUID** instance.
* **path** - Matches any non-empty string, including the path separator, **'/'**. This allows you to match against a complete URL path rather than a segment of a URL path as with **str**.

To view the customer's order details in our project, we use dynamic URL. **str** is used as a path converter in the `urls.py` and it is passed as a parameter in the `customer` function and perform database queries to retrieve the values and display it in the form of a table in customer page.
**urls.py**
```
urlpatterns = [
    path('', views.home, name='home'),
    path('products/', views.products, name='products'),
    path('customer/<str:pk_task>/', views.customer, name='customer'),
]
```
**views.py**
```
def customer(request, pk_task):
	customer = Customer.objects.get(id=pk_task)
	orders = customer.order_set.all()
	order_count = orders.count()
	context = {'customer':customer,'orders':orders, 'order_count':order_count}
	return render(request,'accounts/customer.html',context)
```
**customer.html**
```
{% for order in orders %}

	<tr>
		<td>{{order.product}}</td>
		<td>{{order.product.category}}</td>
		<td>{{order.date_created}}</td>
		<td>{{order.status}}</td>
		<td><a class="btn btn-sm btn-info" href="">Update</a> </td>
		<td><a class="btn btn-sm btn-danger" href="">Remove</a> </td>
	</tr>
				
{% endfor %}
```

## Django CRUD (Create, Retrieve, Update, Delete) Function Based Views
In general CRUD means performing Create, Retrieve, Update and Delete operations on a table in a database.

* **Create** – create or add new entries in a table in the database.
* **Retrieve** – read, retrieve, search, or view existing entries as a list(List View) or retrieve a particular entry in detail (Detail View)
* **Update** – update or edit existing entries in a table in the database
* **Delete** – delete, deactivate, or remove existing entries in a table in the database

We need to create a modelForm for a model.
```
from django.forms import ModelForm
from . models import *

class OrderForm(ModelForm):
	class Meta:
		model = Order
		fields = '__all__'
		
```
For need to create a view for update, delete and create operations.

**Create View**
```
def createOrder(request):

	form = OrderForm()
	if request.method=='POST':
		form = OrderForm(request.POST)
		if form.is_valid():
			form.save()
			return redirect('/')
			
	context = {'form':form}
	return render(request, 'accounts/order_form.html',context)
```
To use the **redirect()** we need to import it from `django.shortcuts`. **is_valid()** will check the validation of the form. **save()** will save the order in the database.

**Update View**
```
def updateOrder(request, pk):

	order = Order.objects.get(id=pk)
	form = OrderForm(instance=order)

	if request.method == 'POST':
		form = OrderForm(request.POST, instance=order)
		if form.is_valid():
			form.save()
			return redirect('/')

	context = {'form':form}
	return render(request, 'accounts/order_form.html', context)	
```
**Delete View**
```
def deleteOrder(request, pk):
	order = Order.objects.get(id=pk)
	if request.method == "POST":
		order.delete()
		return redirect('/')

	context = {'item':order}
	return render(request, 'accounts/delete.html', context)
```
Also, we need to create a template for creating and updating orders and for deleting orders.

## Cross Site Request Forgery protection
The CSRF middleware and template tag provides easy-to-use protection against Cross Site Request Forgeries. This type of attack occurs when a malicious website contains a link, a form button or some JavaScript that is intended to perform some action on your website, using the credentials of a logged-in user who visits the malicious site in their browser.

## How to use it
To take advantage of CSRF protection in your views, follow these steps:

1. The CSRF middleware is activated by default in the **MIDDLEWARE** setting. If you override that setting, remember that **'django.middleware.csrf.CsrfViewMiddleware'** should come before any view middleware that assume that CSRF attacks have been dealt with.

  If you disabled it, which is not recommended, you can use **csrf_protect()** on particular views you want to protect (see below).

2. In any template that uses a POST form, use the **csrf_token** tag inside the <**form**> element if the form is for an internal URL, e.g.:
  ```
  <form method="post">{% csrf_token %}
  ```
  This should not be done for POST forms that target external URLs, since that would cause the CSRF token to be leaked, leading to a vulnerability.

3. In the corresponding view functions, ensure that **RequestContext** is used to render the response so that **{% csrf_token %}** will work properly. 

**order_form.html**
```
{% extends 'accounts/main.html' %} 
{% load static %} 
{% block content %} 

<form action="" method="POST">
  {% csrf_token %}
  {{form}}

  <input type="submit" name="Submit">
</form>
{% endblock %}
```
**delete.html**
```
{% extends 'accounts/main.html' %}
{% load static %}
{% block content %}

<p>Are you sure you want to delete "{{item}}"?</p>
<form action="{% url 'delete_order' item.id  %}" method="POST">
  {% csrf_token %}
  <a href="{% url 'home' %}">Cancel</a>
  <input type="submit" name="Confirm">
</form>
{% endblock %}
```
