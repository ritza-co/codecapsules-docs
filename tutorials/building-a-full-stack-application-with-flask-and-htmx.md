---
description: >-
  Create a full stack application with Flask and HTMx to build a lightweight
  interactive site.
cover: .gitbook/assets/flask-htmx-cover.png
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Building a Full Stack Application with Flask and HTMx

This tutorial explains how to build an application with Flask and HTMx. If you want to host an HTMx and Flask application on our PaaS, you can find a short deployment guide here that uses the same pro

Recent trends in the modern web saw single page frameworks like React.js and Angular take over traditional multipage websites, mainly due to the lack of interactivity offered by HTML. It is worth noting however, that single page applications (SPAs) brought this interactivity at the cost of added complexity.

This is where a new HTML extension called HTMx enters and shines. HTMx gives traditional HTML sites more interactivity while keeping things simple, as it allows you to make requests from any HTML element and not just `<a>` and `<form>` tags. But this is not HTMx’s only benefit. Other benefits include:

* Making it possible to perform partial page reloads in HTML
* Support for PUT and DELETE methods in addition to GET and POST
* Not being limited to click and submit event triggers only
* Lightweight set up – no additional dependencies need to be installed to get it working

In this tutorial, we’ll explore the benefits of HTMx by building a full stack application using Flask and HTMx. Our application will be a book recommendation app that supports CRUD functionality. The final app will look a bit like this:

<figure><img src=".gitbook/assets/building-a-full-stack-application-with-flask-and-htmx/flask-htmx.png" alt=""><figcaption></figcaption></figure>

### Overview and Requirements

After building our application, you’ll want to deploy it to production so you can show it to friends and family. You will therefore need the following to complete the tutorial:

* Git set up and installed, and a registered GitHub account
* Python 3 installed
* An IDE or text editor of your choice

### Setting up the Project

With all the requirements in place, we can go ahead and set up our project. Let’s start by creating the project folder and then we can set up a virtual environment within the project folder.

#### Create Project Folder

Let’s create a folder to house our application’s source code. Run the commands below in the terminal to create the folder and navigate into it.

```
mkdir flask-htmx
cd flask-htmx
```

From here onwards, the `flask-htmx` directory will be referred to as the project’s root folder.

#### Creating a Virtual Environment

A virtual environment allows you to isolate the packages required to develop Python applications from your computer system. We recommend you use a new virtual environment for every application you develop so as to not corrupt dependencies for other applications.

In the terminal run the following command to create a virtual environment within the project's root folder:

```
python3 -m venv env
```

To activate the virtual environment, enter either of the following:

**MacOS**

```
source env/bin/activate
```

**Windows**

```
.\env\Scripts\activate
```

After activating the virtual environment, the name `env` should appear in brackets on the leftmost part of your terminal in your current line. This signals that the activation was successful.

<figure><img src=".gitbook/assets/building-a-full-stack-application-with-flask-and-htmx/venvactive.png" alt=""><figcaption></figcaption></figure>

#### Installing Dependencies

We can now install our required packages to the virtual environment we activated in the previous step. Run the command below:

```sh
pip3 install flask flask-sqlalchemy gunicorn
```

You might notice there’s no dependency for HTMx in our dependency list. This is because it will be added as a script tag inside the head of our HTML templates.

#### Initialize an Empty Git Repository

While in the project’s root folder, enter the command `git init` to initialize a `git` repository. This will allow you to track changes to your app as you build it.

Create a `.gitignore` file and add the line below to it:

```
env/
```

This excludes the `env` folder from being tracked by Git, as we only want to track changes in our project files.

#### Linking to GitHub

Head over to [GitHub](https://github.com/) and create a new repository. Then, in your project's root folder, run the command below from the terminal, replacing `username` and `repository_name` with your own values from GitHub.

```sh
git remote add origin git@github.com:username/repository_name.git
```

This will link your local repository to the one on GitHub.

### Building the HTMx Frontend

With the set up complete, we can now begin building our app. We will start with the HTMx frontend, and for this you need to create an `app/templates` folder inside the project’s root folder.

Next, create an `index.html` file inside the `templates` folder, and populate it with the code below:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Flask HTMX ALPINE App</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/css/bootstrap.min.css" rel="stylesheet"
        integrity="sha384-giJF6kkoqNQ00vy+HMDP7azOuL0xtbfIcaT9wjKHr8RbDVddVHyTfAAsrekwKmP1" crossorigin="anonymous">
    <!-- HTMX -->
    <script src="https://unpkg.com/htmx.org@1.5.0"></script>
    <style>
        body{
            padding: 20px;
        }
        table {
            font-family: arial, sans-serif;
            border-collapse: collapse;
            width: 100%;
        }

        tr.htmx-swapping td {
            opacity: 0;
            transition: opacity 0.5s ease-out;
        }

        td, th {
            border: 1px solid #383737;
            text-align: left;
            padding: 8px;
        }

        tr:nth-child(even) {
            background-color: #dddddd;
        }
    </style>
</head>

<!-- Place <body> </body> code here -->

</html>
```

There’s not much going on in the code snippet above, except for line 5 and 8, which are responsible for loading Bootstrap and HTMx into our `index.html` page. This gives you the power to build an interactive page just by including the `<script>` tag that links to HTMx, without needing to install any `npm` packages like with most SPAs. This is how HTMx allows you to build sites that are more lightweight compared to SPA frameworks.

The code in between the `<style>` tag adds CSS to style our frontend to make it more visually appealing. Now let's add code that will be rendered in the body tag of our page. Copy and paste the code below underneath the \</head> tag:

```html
<body>
    <h1>Book Recommendations</h1>
    <form hx-post="/submit" hx-swap="beforeend" hx-target="#new-book" class="mb-3">
        <input type="text" placeholder="Book Title" name="title" class="form-control mb-3" />
        <input type="text" placeholder="Book Author" name="author" class="form-control mb-3" />
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>

    <table class="table">
        <thead>
          <tr>
            <th scope="col">Book Title</th>
            <th scope="col">Book Author</th>
          </tr>
        </thead>
        <tbody id="new-book" hx-target="closest tr" hx-swap="outerHTML swap:0.5s"> 
            {% raw %}
{% for book in books %}
            <tr>
                <td>{{book.Book.title}}</td>
                <td>{{book.Author.name}}</td>
                <td>
                    <button class="btn btn-primary" 
                        hx-get="/get-edit-form/{{book.Book.book_id}}">
                        Edit Title
                    </button>
                </td>
                <td>
                    <button hx-delete="/delete/{{book.Book.book_id}}" class="btn btn-primary">Delete</button>
                </td>
            </tr>
            {% endfor %}
{% endraw %}
        </tbody>
    </table>
</body>
```

There are a couple of attributes here that aren't used in traditional HTML. Let's go over them one by one:

* **hx-\[http method]** – Examples of this attribute include `hx-post`, `hx-get`, `hx-put` and `hx-delete`. This is the HTMx way of denoting what type of request should be sent on form submission or when a request firing event is triggered. These attributes accept the request route as an argument. In the case of our form, we use the `/submit` route, while the table buttons send requests to the `/delete` and `/get-edit-form` routes.
* **hx-target** – This attribute accepts the `id` of the element you want to update after a successful request or when an event is triggered. Take note of the preceding # that's written before the id value.

You might have noticed that we didn't use an `id` value in the table, but used a value of closest `tr` instead. This swaps the closest table row with the HTML that will be returned by the request when an action is triggered. The closest row will always be the same row in which an event or request was triggered, either by the "Edit Title" button or the "Delete" button.

* **hx-swap** – The [hx-swap attribute](https://htmx.org/docs/#swapping) allows you to specify how you want to partially reload the page or swap elements with new ones. It updates the UI in the section specified in the hx-target attribute.

In our form, we used the beforeend value to tell HTMx that we want to append the result of the request after the last child in the target element, which is the table with id=new-book.

In the table, however, we used the outerHTML value to denote that we want to swap the entire `<tr>` element with the returned content.

The table rows are dynamically generated using Jinja2's `{% for book in books %}` loop syntax, which iterates through each book in the books list passed from our Flask backend.&#x20;

A full list of acceptable hx-swap values can be viewed [here](https://htmx.org/docs/#swapping).

### Building the Flask Backend

We can now dive into building the backend of our app. Start by creating a `run.py` file in the project’s root folder and populate it with the code below:

```py
from app import app

if __name__ == "__main__":
    app.run()
```

This is the point of entry for any server wishing to run our app.

#### Declare and Initialize `app` Module

Create an `__init__.py` file in the `app` folder and populate it with the code below:

```py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
import os

persistent_path = os.getenv("PERSISTENT_STORAGE_DIR", os.path.dirname(os.path.realpath(__file__)))

app = Flask(__name__)

db_path = os.path.join(persistent_path, "sqlite.db")

app.config["SQLALCHEMY_DATABASE_URI"] = f'sqlite:///{db_path}'
app.config["SQLALCHEMY_ECHO"] = False
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False

db = SQLAlchemy()

from app import views
from app import models

db.init_app(app)

with app.app_context():
    db.create_all() 
```

Here we declare our app object using the Flask package we installed earlier. We’ll be using a SQLite database, which we configure using the `app.config` lines.

We use SQLAlchemy to declare the database object we’ll interact with, as it allows us to read and write to the database using object notation, which is more familiar than raw SQL statements. It is important to import the models only after declaring the database object so that their respective tables are included in the database when it’s created. The last two lines handle the initialization of the database and the creation of all relevant tables in the database based on the models that are imported.

#### Register App Models

The next step is to create the `models.py` module we imported earlier in the `__init__.py` file. Create a file named `models.py` inside the `app` folder and populate it with the code below:

```py
from app import db

class Author(db.Model):
    author_id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String)
    books = db.relationship("Book", backref="author")

    def __repr__(self):
        return '<Author: {}>'.format(self.books)

class Book(db.Model):
    book_id = db.Column(db.Integer, primary_key=True)
    author_id = db.Column(db.Integer, db.ForeignKey("author.author_id"))
    title = db.Column(db.String)
```

Here we declare the two models we’re going to be saving in our database, which are `Author` and `Book`. It is worth noting the one-to-many relationship between "author" and "books", as an author can have many Books but each book can only have a single author in the context of this application. We denote this relationship by using a foreign key in the `author_id` field of the book and a `backref` in the books field of an author.

#### Create Views

The last part in building the backend of our app is to create the views or routes that the frontend will be interacting with. Create a `views.py` file inside the `app` folder and add the code below to it:

```py
from app import app, db
from flask import render_template, request, jsonify
from app.models import Author, Book

@app.route("/", methods=["GET"])
def home():
    books = db.session.query(Book, Author).filter(Book.author_id == Author.author_id).all()
    return render_template("index.html", books=books)
```

In the code snippet above, we’ve added the index route and bound the home function to it. The home function first queries the database to get a list of all books before returning the `index.html` template that’s populated with the list of books.

Add the code below to add the `/submit` route to our app’s `views.py` file:

```py
@app.route("/submit", methods=["POST"])
def submit():
    global_book_object = Book()

    title = request.form["title"]
    author_name = request.form["author"]

    author_exists = db.session.query(Author).filter(Author.name == author_name).first()
    print(author_exists)
    # check if author already exists in db
    if author_exists:
        author_id = author_exists.author_id
        book = Book(author_id=author_id, title=title)
        db.session.add(book)
        db.session.commit()
        global_book_object = book
    else:
        author = Author(name=author_name)
        db.session.add(author)
        db.session.commit()

        book = Book(author_id=author.author_id, title=title)
        db.session.add(book)
        db.session.commit()
        global_book_object = book

    response = f"""
    <tr>
        <td>{title}</td>
        <td>{author_name}</td>
        <td>
            <button class="btn btn-primary"
                hx-get="/get-edit-form/{global_book_object.book_id}">
                Edit Title
            </button>
        </td>
        <td>
            <button hx-delete="/delete/{global_book_object.book_id}"
                class="btn btn-primary">
                Delete
            </button>
        </td>
    </tr>
    """
    return response
```

When a person submits a new book, this is the route that’s called. The logic first checks if the author already exists in the database and if so, saves the book with the author’s `author_id`. Otherwise, it creates a new author and then saves the book. As HTMx expects an HTML response, the `submit` method responds with an HTML table row that updates the list of books on the frontend. The new entry will be for the recently added book.

Next, let’s add the code for the `/delete` route. Copy and paste the code below:

```py
@app.route("/delete/<int:id>", methods=["DELETE"])
def delete_book(id):
    book = Book.query.get(id)
    db.session.delete(book)
    db.session.commit()

    return ""
```

The first thing you may have noticed about this route is the `id` query parameter it accepts. This allows us to know which object to delete. After deleting the book, we return an empty string, which causes the row we deleted in the frontend to disappear, as it is swapped for "nothing".

We now have routes for creating, reading, and deleting books. It’s time to add views associated with updating book entries in order to complete our app’s CRUD functionality. Add the code below to `views.py` to add logic for updating book entries to your app:

```py
@app.route("/get-edit-form/<int:id>", methods=["GET"])
def get_edit_form(id):
    book = Book.query.get(id)
    author = Author.query.get(book.author_id)

    response = f"""
    <tr hx-trigger='cancel' class='editing' hx-get="/get-book-row/{id}">
  <td><input name="title" value="{book.title}"/></td>
  <td>{author.name}</td>
  <td>
    <button class="btn btn-primary" hx-get="/get-book-row/{id}">
      Cancel
    </button>
    <button class="btn btn-primary" hx-put="/update/{id}" hx-include="closest tr">
      Save
    </button>
  </td>
    </tr>
    """
    return response

@app.route("/get-book-row/<int:id>", methods=["GET"])
def get_book_row(id):
    book = Book.query.get(id)
    author = Author.query.get(book.author_id)

    response = f"""
    <tr>
        <td>{book.title}</td>
        <td>{author.name}</td>
        <td>
            <button class="btn btn-primary"
                hx-get="/get-edit-form/{id}">
                Edit Title
            </button>
        </td>
        <td>
            <button hx-delete="/delete/{id}"
                class="btn btn-primary">
                Delete
            </button>
        </td>
    </tr>
    """
    return response

@app.route("/update/<int:id>", methods=["PUT"])
def update_book(id):
    db.session.query(Book).filter(Book.book_id == id).update({"title": request.form["title"]})
    db.session.commit()

    title = request.form["title"]
    book = Book.query.get(id)
    author = Author.query.get(book.author_id)

    response = f"""
    <tr>
        <td>{title}</td>
        <td>{author.name}</td>
        <td>
            <button class="btn btn-primary"
                hx-get="/get-edit-form/{id}">
                Edit Title
            </button>
        </td>
        <td>
            <button hx-delete="/delete/{id}"
                class="btn btn-primary">
                Delete
            </button>
        </td>
    </tr>
    """
    return response
```

There’s more than one view for the update logic, and we’ll see why shortly. The `/get-edit-form` route is called when a user clicks on the "Edit Title" button on the frontend, and it returns a form for updating the selected book. If the user decides to cancel this action, the `/get-book-row` route is called and it returns a table row with the unedited book entry.

If the user goes through with updating the book title, then the `/update` route is called. The `update_book` function that’s bound to the `/update` route will update the book title based on the `id` supplied to it as a query parameter. When the update is complete, the method returns an HTML table row with the updated book title.

### Running our App

Our app is ready to be tested. Navigate to the project’s root folder in a terminal and run the following command: `python3 run.py`. This should start up a development server on port 5000. Open your browser at `http://127.0.0.1:5000/` and you should see your app running:

<figure><img src=".gitbook/assets/flask-htmx (2).png" alt=""><figcaption></figcaption></figure>

Once you are satisfied with the app, you can commit and push your changes to GitHub with the following commands.

```sh
git add . 
git commit -m 'commit message'
git push origin
```

We’ve shown you how to build a full-stack Flask HTMx application from scratch, and you should be able to deploy this basic version, but you may like to consider adding more functionality to enhance our app’s features. We recommend you check out [Alpine.js](https://alpinejs.dev/), a lightweight JavaScript framework that works well with `HTMx` to make sites that are more powerful yet still lightweight.

You can find the complete code shown above in [this GitHub repository](https://github.com/codecapsules-io/demo-flask-htmx).
