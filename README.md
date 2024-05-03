# book-store

Start the Django project

    django-admin startproject book_store

CD into the new folder

    cd .\my_site\

Start a new app inside the project app

    python manage.py startapp book_outlet

Start database migrations

    python manage.py makemigrations

Run database migrations

    python manage.py migrate

Start a terminal to work with the Django project and its database

    python manage.py shell

> from book_outlet.models import Book
>
> lord_of_the_rings = Book(title="Lord of the Rings", rating=4)
>
> lord_of_the_rings.save()
