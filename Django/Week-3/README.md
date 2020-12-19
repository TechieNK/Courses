# DjangoProject
## Week-3

## Database queries
Once you’ve created your data models, Django automatically gives you a database-abstraction API that lets you create, retrieve, update and delete objects.  

## When QuerySets are evaluated
Internally, a **QuerySet** can be constructed, filtered, sliced, and generally passed around without actually hitting the database. No database activity actually occurs until you do something to evaluate the queryset.

You can evaluate a **QuerySet** in the following ways:

* **Iteration**: A **QuerySet** is iterable, and it executes its database query the first time you iterate over it. For example, this will print the headline of all entries in the database:
```
for e in Entry.objects.all():
    print(e.headline)
```    
**Note**: Don’t use this if all you want to do is determine if at least one result exists. It’s more efficient to use **exists()**.

* **Slicing**: A **QuerySet** can be sliced, using Python’s array-slicing syntax. Slicing an unevaluated **QuerySet** usually returns another unevaluated **QuerySet**, but Django will execute the database query if you use the “step” parameter of slice syntax, and will return a list. Slicing a **QuerySet** that has been evaluated also returns a list.

Also note that even though slicing an unevaluated **QuerySet** returns another unevaluated **QuerySet**, modifying it further (e.g., adding more filters, or modifying ordering) is not allowed, since that does not translate well into SQL and it would not have a clear meaning either.

* **Pickling/Caching**: See the following section for details of what is involved when pickling QuerySets. The important thing for the purposes of this section is that the results are read from the database.

* **repr()** A **QuerySet** is evaluated when you call **repr()** on it. This is for convenience in the Python interactive interpreter, so you can immediately see your results when using the API interactively.

* **len()** A **QuerySet** is evaluated when you call **len()** on it. This, as you might expect, returns the length of the result list.

**Note**: If you only need to determine the number of records in the set (and don’t need the actual objects), it’s much more efficient to handle a count at the database level using SQL’s **SELECT COUNT(*)**. Django provides a **count()** method for precisely this reason.

* **list()** Force evaluation of a **QuerySet** by calling **list()** on it. For example:
```
entry_list = list(Entry.objects.all())
```
* **bool()**: Testing a **QuerySet** in a boolean context, such as using **bool()**, **or**, **and** or an **if** statement, will cause the query to be executed. If there is at least one result, the **QuerySet** is **True**, otherwise **False**. For example:
```
if Entry.objects.filter(headline="Test"):
   print("There is at least one Entry with the headline Test")
```
**Note**: If you only want to determine if at least one result exists (and don’t need the actual objects), it’s more efficient to use **exists()**.

## Pickling QuerySets
If you **pickle** a **QuerySet**, this will force all the results to be loaded into memory prior to pickling. Pickling is usually used as a precursor to caching and when the cached queryset is reloaded, you want the results to already be present and ready for use (reading from the database can take some time, defeating the purpose of caching). This means that when you unpickle a **QuerySet**, it contains the results at the moment it was pickled, rather than the results that are currently in the database.

If you only want to pickle the necessary information to recreate the **QuerySet** from the database at a later time, pickle the **query** attribute of the **QuerySet**. You can then recreate the original **QuerySet** (without any results loaded) using some code like this:
```
>>> import pickle
>>> query = pickle.loads(s)     # Assuming 's' is the pickled string.
>>> qs = MyModel.objects.all()
>>> qs.query = query            # Restore the original 'query'.
```
The **query** attribute is an opaque object. It represents the internals of the query construction and is not part of the public API.

## Methods that return new QuerySets
Django provides a range of **QuerySet** refinement methods that modify either the types of results returned by the **QuerySet** or the way its SQL query is executed.

* **filter()**: Returns a new **QuerySet** containing objects that match the given lookup parameters.
* **exclude()**: Returns a new **QuerySet** containing objects that do not match the given lookup parameters.

  This example excludes all entries whose **date** is later than 2005-1-3 AND whose **headline** is “Hello”:
  ```
  Entry.objects.exclude(date__gt=datetime.date(2005, 1, 3), headline='Hello')
  ```
  In SQL terms, that evaluates to:
  ```
  SELECT ...
  WHERE NOT (date > '2005-1-3' AND headline = 'Hello')
  ```
* **annotate()**: Annotates each object in the **QuerySet**. An expression may be a simple value, a reference to a field on the model (or any related models), or an aggregate expression (averages, sums, etc.) that has been computed over the objects that are related to the objects in the **QuerySet**.

  Each argument to annotate() is an annotation that will be added to each object in the **QuerySet** that is returned.
  
  Annotations specified using keyword arguments will use the keyword as the alias for the annotation. Anonymous arguments will have an alias generated for them based upon the name of the aggregate function and the model field that is being aggregated. Only aggregate expressions that reference a single field can be anonymous arguments. Everything else must be a keyword argument.
* **order_by()**: By default, results returned by a **QuerySet** are ordered by the ordering tuple given by the ***ordering** option in the model’s **Meta**. You can override this on a per-**QuerySet** basis by using the **order_by** method.
* **reverse()**: Use the **reverse()** method to reverse the order in which a queryset’s elements are returned. Calling **reverse()** a second time restores the ordering back to the normal direction.
* **distinct()**: Returns a new **QuerySet** that uses **SELECT DISTINCT** in its SQL query. This eliminates duplicate rows from the query results.

  By default, a **QuerySet**will not eliminate duplicate rows. In practice, this is rarely a problem, because simple queries such as **Blog.objects.all()** don’t introduce the possibility of duplicate result rows. However, if your query spans multiple tables, it’s possible to get duplicate results when a **QuerySet** is evaluated. That’s when you’d use **distinct()**.
* **values()**: Returns a **QuerySet** that returns dictionaries, rather than model instances, when used as an iterable.

  Each of those dictionaries represents an object, with the keys corresponding to the attribute names of model objects.
* **values_list()**: This is similar to **values()** except that instead of returning dictionaries, it returns tuples when iterated over. Each tuple contains the value from the respective field or expression passed into the **values_list()** call — so the first item is the first field, etc.

You can view the <a href="https://docs.djangoproject.com/en/3.1/ref/models/querysets/">full list here</a>
