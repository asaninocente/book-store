# book-store

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