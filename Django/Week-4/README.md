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
