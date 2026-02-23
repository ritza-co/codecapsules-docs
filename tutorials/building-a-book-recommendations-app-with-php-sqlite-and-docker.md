---
cover: .gitbook/assets/php-book-app-cover.jpg
coverY: 0
coverHeight: 425
layout:
  width: default
  cover:
    visible: true
    size: full
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
  metadata:
    visible: true
  tags:
    visible: true
---

# Building a Book Recommendations App With PHP, SQLite, and Docker

PHP is one of the first technologies that made dynamic web applications possible, and it’s still widely used today. In this tutorial, we’ll look at how to build a CRUD application with PHP and SQLite. We’ll build a basic book recommendation application where we can **C**reate new entries or **R**ead, **U**pdate, or **D**elete existing ones. Nearly all applications rely on these four CRUD operations, so you’ll be able to extend this application to do anything else you want.

Here’s what the final app will look like:

![final app](.gitbook/assets/building-a-book-recommendations-app-with-php-sqlite-and-docker/final-app.png)

### Requirements <a href="#requirements" id="requirements"></a>

You will need the following to complete the tutorial:

* Docker installed locally.
* IDE or text editor of your choice.
* A local PHP developer environment (optional).

You can do all the development using the Docker environment that we’ll create as part of the tutorial, but it can be easier to run and debug code locally so if you haven’t used PHP before and don’t want to do the set-up, you can rely only on Docker.

To deploy the application to Code Capsules, you’ll also need:

* A [GitHub](https://github.com/) account and Git installed locally.
* A [Code Capsules](https://codecapsules.io/) account.

### Project Setup <a href="#project-set-up" id="project-set-up"></a>

Let’s start by creating a project folder that will house all our files.

In a terminal, navigate to the directory you’ll be keeping the application files in. Run the commands below to create the project folder and navigate into it.

```
mkdir book-recommendations
cd book-recommendations
```

### Building the Frontend <a href="#building-the-frontend" id="building-the-frontend"></a>

Let’s begin by building our app’s index page. This page will use PHP and HTML, as it will contain both static and dynamic content. Create a file named `index.php` in the project root folder and populate it with the code below:

```
<!DOCTYPE html>
<html lang="en">
   <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>Book Recommendations</title>
      <link rel="stylesheet" href="https://fonts.xz.style/serve/inter.css" />
      <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@exampledev/new.css@1.1.2/new.min.css" />
   </head>
   <body>
   <header>
      <h1>Book Recommendations CRUD demo</h1>
   </header>
      <table>
         <thead>
            <tr>
               <th>Book Title</th>
               <th>Author</th>
               <th colspan="2">Action</th>
            </tr>
         </thead>
      </table>
   </body>
</html>
```

This builds out a basic HTML page. We add links in the header to [xz/fonts](https://fonts.xz.style/), a CDN to get open-source fonts and [New CSS](https://newcss.net/), a classless CSS framework that makes HTML look better out of the box without having to add specific class names like you would with a framework like Bootstrap or Tailwind.

It then sets up a table structure that we’ll use PHP to populate later.

### Adding a Docker File and Running our App <a href="#adding-a-docker-file-and-running-our-app" id="adding-a-docker-file-and-running-our-app"></a>

Even though our app doesn’t do anything yet, let’s run it to see our progress so far. We’ll use Docker for this. In the same project directory, create a file called exactly `Dockerfile` (note the capital D and no file extension), and add the following code.

```
FROM php:8.0-apache
WORKDIR /var/www/html

COPY . .
EXPOSE 80
```

This pulls an official Docker container which already has the PHP language installed and integrated with Apache, a web server. It copies all files from the local directory (in our case, just `index.php` for now), and exposes port 80, which is the port that Apache is set to serve files on.

Now run the following command in your terminal.

```
docker build . -t book-app && docker run -p 8000:80 book-app
```

This builds a Docker image from the `Dockerfile` in the current directory and gives it `book-app` as a tag. The second command (after `&&`) runs the container, and maps our local port 8000 to the Docker port 80. Once it’s running, you can visit `http://localhost:8000` in your web browser to see the application.

![frontend](.gitbook/assets/building-a-book-recommendations-app-with-php-sqlite-and-docker/frontend.png)

Hit `Ctrl + C` in the terminal window running Docker to stop the server.

### Adding Books <a href="#adding-books" id="adding-books"></a>

To allow the user to add new books, we’ll need a form with an input. Let’s build that now.

Add the following code to your `index.php` file, above the existing table definition.

```
<h2>Add Book</h2>
<form method="POST">
   <label for="title">Book Title</label> <br>
   <input type="text" name="title"><br>
   <label for="author">Author</label> <br>
   <input type="text" name="author"><br>
   <input type="hidden" name="action" value="create"><br>
   <button type="submit" name="save">Save</button><br>
</form>
<h2>Books</h2>
```

This sets up a form with inputs and a submit button. Note the hidden field with a value of “create”, which we’ll be using later to differentiate between different actions, such as creating, updating, or deleting books.

If you run the app again, you’ll see something like the following.

![frontend with form](.gitbook/assets/building-a-book-recommendations-app-with-php-sqlite-and-docker/frontend-with-form.png)

Now you can type in a book and author name and press the “Save” button, but then the app will crash as we haven’t built the backend yet. Let’s do that next.

### Building the Backend <a href="#building-the-backend" id="building-the-backend"></a>

Next, we’ll create an `app.php` file to handle the backend logic and database connection for our application. Create this file and add the following code.

```
<?php
$database_name = "/tmp/" . "books.db";
$db = new SQLite3($database_name);
$query = "CREATE TABLE IF NOT EXISTS books (id INTEGER PRIMARY KEY, title STRING, author STRING)";
$db->exec($query);

if ($_POST) {
    if ($_POST["action"] == "create") {
        $title = $_POST["title"];
        $author = $_POST["author"];
        $stmt = $db->prepare("INSERT INTO books (title, author) VALUES (:title, :author)");
        $stmt->bindValue(":title", $title, SQLITE3_TEXT);
        $stmt->bindValue(":author", $author, SQLITE3_TEXT);
        $stmt->execute();
    }
}
?>
```

The code above connects to a [SQLite](https://www.sqlite.org/index.html) database when the app is launched. SQLite is a lightweight alternative to full database systems such as PostgreSQL or MySQL. It stores all data in a simple file. It also automatically creates a database if you give it a file that doesn’t exist, so in our case it will create the `books.db` file the first time we run this code.

Note that we use a database directly in the `/tmp` folder of the Docker container. This means you’ll lose all data every time you restart your application. We’ll fix this towards the end of the tutorial when we deploy the application and set up persistent storage.

This code also creates a table for our books if it doesn’t already exist. The `if` statement checks if there’s any data in `$_POST`, which will be populated from the form we defined in `index.php`, and then we insert this data into the database. Note that we use [parameterized queries](https://www.php.net/manual/en/sqlite3.prepare.php) instead of basic string concatenation to include the dynamic user input in the `INSERT INTO` statement. This is to prevent [SQL injection](https://owasp.org/www-community/attacks/SQL_Injection), which is a common vulnerability where a malicious user hacks your database by modifying the database inputs.

To use this code from the main `index.php` file, add the following lines to the top.

```
<?php
include "app.php";
?>
```

This imports all the code into `index.php`, as if you had written in that file. We keep most of the PHP code in a separate file to make our codebase better organized.

You can run the application again now, and you’ll see that you’re able to insert books using the form. However, we aren’t ever reading the books from the database again, so they’ll just disappear. Let’s add some more logic to retrieve any saved books from the database and display them to the user.

### Retrieving the Books from the Database <a href="#retrieving-the-books-from-the-database" id="retrieving-the-books-from-the-database"></a>

We’ll display our books to the user by grabbing them all from the database, looping through them, and adding a table row for each entry.

In the `app.php` file, add the following function to the top of the file.

```
function getBooks($db) {
    $query = "SELECT * FROM books";
    $results = $db->query($query);
    return $results;
}
```

This retrieves the books from the database and returns an array containing all of them. Update the **Books** section of the `index.php` file with the following code.

```
<h2>Books</h2>
<table>
    <thead>
        <tr>
            <th>Book Title</th>
            <th>Author</th>
            <th colspan="2">Action</th>
        </tr>
    </thead>
    <?php
    $results = getBooks($db);
    while ($row = $results->fetchArray()):
    ?>
    <tr>
        <td><?php echo $row["title"]; ?></td>
        <td><?php echo $row["author"]; ?></td>
        <td>
            <button onclick="update_book(
                <?php echo $row["id"] ?>,
                '<?php echo $row["title"] ?>',
                '<?php echo $row["author"] ?>'
            )">Edit</button>
        </td>
        <td>
            <button onclick="delete_book(
                <?php echo $row["id"] ?>,
                '<?php echo $row["title"] ?>',
                '<?php echo $row["author"] ?>'
            )">Delete</button>
        </td>
    </tr>
    <?php endwhile; ?>
</table>
```

Here we use our `getBooks` function to retrieve all the books and then a `while` loop to iterate through each one. We add each book as a new table row, displaying the title and the author in their own columns. We also add more columns with an “Edit” and “Delete” button for each book. The buttons call JavaScript functions (that we haven’t written yet), passing in the ID, title, and author of the book that the user wants to edit or delete.

If you run the app again, you’ll see that now any books that you add automatically show up in the table. The Edit and Delete buttons don’t work yet, though, so let’s fix that.

### Adding Edit and Delete Functionality <a href="#adding-edit-and-delete-functionality" id="adding-edit-and-delete-functionality"></a>

To avoid our table getting too messy, we’ll use some basic JavaScript to edit and delete books, along with some hidden forms at the top of our page. Right after the opening `<body>` tag in `index.php`, add the following forms.

```
<form id="updateForm" method="POST">
    <input type="hidden" name="update_id" id="update_id">
    <input type="hidden" name="new_title" id="new_title">
    <input type="hidden" name="new_author" id="new_author">
    <input type="hidden" name="action" value="update">
</form>

<form id="deleteForm" method="POST">
    <input type="hidden" name="action" value="delete">
    <input type="hidden" name="delete_id" id="delete_id">
</form>
```

These are two forms consisting only of hidden input fields, so they won’t be visible to the user. We’ll populate the values and submit them using JavaScript. The first form has values for `new_title` and `new_author` so we can update the database with new values supplied by the user. The “delete” form only has the book ID, as that’s all we need to remove it from the database.

We need matching JavaScript functions to use these forms, so add the following code to the `<head>` section of your `index.php` file.

```
<script>
function update_book(id, title, author) {
    let new_title = prompt("Please enter new title:", title);
    if (new_title == null || new_title == "") { return; }
    let new_author = prompt("Please enter new author:", author);
    if (new_author == null || new_author == "") { return; }
    document.getElementById("new_title").value = new_title;
    document.getElementById("new_author").value = new_author;
    document.getElementById("update_id").value = id;
    document.getElementById("updateForm").submit();
}

function delete_book(id, title, author) {
    let is_sure = "Deleting book '" + title + "' by '" + author + "'. Are you sure?";
    if (confirm(is_sure) == true) {
        document.getElementById("delete_id").value = id;
        document.getElementById("deleteForm").submit();
    }
}
</script>
```

We pass variables for the ID, title, and author to both functions. Although the existing title and ID are not strictly necessary, it’s nice to show them to the user for reference when they are entering the new information or as a confirmation for the delete function. The update function prompts the user for a new title and author and then submits the update form, while the delete function confirms if the user really wants to delete that entry and then submits the delete form.

Finally, to make these work on the backend, update the if statement in the `app.php` file to look as follows:

```
if ($_POST) {
    if ($_POST["action"] == "create") {
        // Insert new book
        $title = $_POST["title"];
        $author = $_POST["author"];
        $stmt = $db->prepare("INSERT INTO books (title, author) VALUES (:title, :author)");
        $stmt->bindValue(":title", $title, SQLITE3_TEXT);
        $stmt->bindValue(":author", $author, SQLITE3_TEXT);
        $stmt->execute();
    }
    elseif ($_POST["action"] == "update") {
        $id = $_POST['update_id'];
        $title = $_POST['new_title'];
        $author = $_POST['new_author'];
        $stmt = $db->prepare("UPDATE books SET title=:title, author=:author WHERE id=:id");
        $stmt->bindValue(':title',$title,SQLITE3_TEXT);
        $stmt->bindValue(':author',$author,SQLITE3_TEXT);
        $stmt->bindValue(':id',$id,SQLITE3_INTEGER);
        $stmt->execute();
    }
    elseif ($_POST["action"] == "delete") {
        $id = $_POST['delete_id'];
        $stmt = $db->prepare('DELETE FROM books WHERE id=:id');
        $stmt->bindValue(':id',$id,SQLITE3_INTEGER);
        $stmt->execute();
    }
}
```

This now handles the update and delete forms we built, calling `UPDATE` or `DELETE` statements on our database as required. Note that we are still using prepared statements to protect against SQL injection.

### Deploying the Application <a href="#deploying-the-application" id="deploying-the-application"></a>

The application should now run fine on your local machine, but let’s deploy it to the internet, so others can use it too. We’ll:

* Create a GitHub repository and push the code to GitHub.
* Create a Docker and Data Capsule on Code Capsules and bind them together.
* Deploy the code to Code Capsules.

Head over to [GitHub](https://github.com/) and create a new repository. Then, in your project’s root folder, run the commands below from the terminal, replacing “username” and “repository\_name” with your own values from GitHub.

```
git init
git add -A
git commit -m "Added book recommendation app files"
git branch -M main
git remote add origin git@github.com:username/repository_name.git
git push -u origin main
```

Your remote repository will now be up-to-date with your local one.

### Deploying to Code Capsules <a href="#deploying-to-code-capsules" id="deploying-to-code-capsules"></a>

The final step is to deploy our app to Code Capsules. We’ll use two capsules for this: a Docker Capsule for the application and a persistent storage Data Capsule for the database, so that our data doesn’t disappear each time the application is restarted.

Change the line where you connect to the database in the `app.php` file to match the following.

```
$database_name = $_ENV["PERSISTENT_STORAGE_DIR"] ."/books.db";
```

In Code Capsules, the `PERSISTENT_STORAGE_DIR` environment variable will point to the Data Capsule once the two capsules are bound together.

Push all of your code changes up to your GitHub repository and ensure that Code Capsules is authorized to read that repository. You can reference this [deployment guide](https://app.gitbook.com/s/xjp0G5hHSJs8nyv5Z5g7/backend/docker/flask-docker-app) to see how to do so in greater detail.

Now create a new Data Capsule and a Docker Capsule in a single Space in Code Capsules. For the Data capsule, choose “A persistent storage mounted directly to your capsule”.

For the Docker capsule, choose your GitHub repository and enter `Dockerfile` for the Dockerfile location. In the configuration tab, set “Network Port” to “80” to match what Apache is running on, and bind the Docker Capsule to the Data Capsule.

![Configuration](.gitbook/assets/building-a-book-recommendations-app-with-php-sqlite-and-docker/configuration.png)

Deploy and build the application, and you should see it running on a custom URL that you can share with the world.
