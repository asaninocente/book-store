# book-store
- Install the "Django" VSCode extension (by Baptiste Dasthenay)
- Ctrl + Shift + P --> "workspace settings" --> add <"python.languageServer": "Pylance">

Start the Django project

    django-admin startproject book_store

CD into the new folder

    cd .\book-store\

Start a new app inside the project app

    python manage.py startapp book_outlet


Run the server

    python manage.py runserver

Start database migrations

    python manage.py makemigrations

Run database migrations

    python manage.py migrate

Start a terminal to work with the Django project and its database

    python manage.py shell

Inserting data:
> from book_outlet.models import Book
>
> lord_of_the_rings = Book(title="Lord of the Rings", rating=4)
>
> lord_of_the_rings.save()

Getting all entries:
> Book.objects.all()

Get a specific record:
> Book.objects.all()[1]
>
> Book.objects.all()[1].author
>
> Book.objects.all()[1].rating

Updating data:
> lotr = Book.objects.all()[1]
>
> lotr.author = "J.R.R. Tolkien"
>
> lotr.is_bestselling = True
>
> lotr.save()
>
> Book.objects.all()[1].author 
>
> Book.objects.all()[1].is_bestselling

Delete data:
> harry_potter = Book.objects.all()[0]
>
> harry_potter.delete()

Create() vs save():  
_Book(title="...").save() === Book.objects.create(title="...")_
> Book.objects.create(title="My Story", rating=2, author="Max", is_bestselling=False)

Querying and filtering data:  
__Get will always get exactly one data entry even if your criteria matches multiples entries. It will return an error if more than one data entry matches the criteria. Recommendation: use the "id" field.__
> Book.objects.get(id=3)  

__Unline get(), filter() can return multiple results.__
> Book.objects.filter(is_bestselling=False, rating=2)  

__To make certain operations, you need to use Django field lookups:__
> Book.objects.filter(rating__lt=3, title__contains="Story")  

__In SQLite, \_\_contains is not case-sensitive.__

'OR' condition
> from django.db.models import Q

If Q condition is used together with non-Q conditions, the latter have to be in the end

If you don't add ".save()" to the creation of an entry, it won't touch the database.  
When you run queries, if you store the query in a variable, it doesn't hit the database. What Python stores is the definition of the query. That means you can chain filters:
> bestsellers = Book.objects.filter(is_bestselling=True)
>
> amazing_bestsellers = bestsellers.filter(rating__gt=4)

Only when you print the variable, Django goes to the database and runs the query. Notice that it doesn't only reach out to the database and get the data, but it also caches the results. It caches them until the database is modified. When queries are chained, that caching helps the query to minimize the effort to get the data. In the following example, even though there are 4 lines of code (2 definitions and 2 prints), there was **only one hit to the database** (print bestsellers):
> print(bestsellers)
>
> print(amazing_bestsellers)

If you run the same query several times but not storing it in a variable, it doesn't cache it, so you hit many times the database:
> print(Book.objects.filter(rating__gt=3))
>
> print(Book.objects.filter(rating__gt=3))

However, if you store it in a variable and print the result as many times as you want, the database is only hit once.
> good_books = Book.objects.filter(rating__gt=3) 
>
> print(good_books)
>
> print(good_books)

Instead of using _{% url 'book-detail' book.id %}_ in the template, you can use _{{ book.get_absolute_url }}_ but need to define the get_absolute_url() function in models.py

If you implement a slug after you created DB records, just _Book.objects.get(title="...").save()_ and the slug will be created.

Using _db_index=True_ increases performance, but you shouldn't turn all your fields into indexes. Really just the ones which you use a lot for querying.

Create a super user to login in /admin

    python manage.py createsuperuser

Working with Relations in the shell:

    from book_outlet.models import Book, Author

> jkrowling = Author(first_name='J.K.', last_name='Rowling')  
>
> jkrowling.save()
>
> hp1 = Book(title='Harry Potter 1', rating=5, is_bestselling =True, author=jkrowling)
>
> hp1.save()
>
> harrypotter = Book.objects.get(title='Harry Potter 1')
>
> harrypotter

_Out[10]: <Book: Harry Potter 1 (5)>_
> harrypotter.author

_Out[11]: <Author: Author object (1)>_

Cross Model Queries:
> books_by_rowling = Book.objects.filter(author__last_name__contains='wling')

> jkr = Author.objects.get(first_name='J.K.')
>
> jkr.book_set.all()

Although Django automatically creates this property on the Author object, you cant set in _models.py_ a custom function to call the inverse of a model relation (through the _related_name_ parameter in the field related between models)
> jkr.books.all()
>
>jkr.books.get(title='Harry Potter 1')
>
> jkr.books.filter(rating__gt=3)

Add data to one-to-one relation:
> from book_outlet.models import Book, Author, Address        
>
> addr1 = Address(street='Some Street', postal_code='12345', city='London')
>
> addr2 = Address(street='Another Street', postal_code='67890', city='New York')
>
> addr1.save()
>
> addr2.save()
>
> jkr = Author.objects.get(first_name='J.K.')
>
> jkr.address = addr1
>
> jkr.save()
>
> Address.objects.all()[0].author.first_name

Add data to many-to-many relation:
> from book_outlet.models import Country, Book
>
> hp1 = Book.objects.all()[0]
>
> hp1.published_countries.all()
>
> germany = Country(name='Germany', code='DE')
>
> germany.save()
>
> mys = Book.objects.all()[1]
>
> mys.published_countries.add(germany)
>
> mys.published_countries.filter(code='DE')

The _add()_ method only exists when you're dealing with many-to-many relations.

Inverse:
> ger = Country.objects.all()[0]
>
> ger.book_set.all()

