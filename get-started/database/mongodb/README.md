# MongoDB

MongoDB provides reliable NoSQL persistent storage for your applications. In this tutorial, we look at how to create a MongoDB instance you can use with your backend applications that are running on Code Capsules and elsewhere.

## Create a MongoDB Data Capsule

Log in to your Code Capsules account and navigate to the Space your MongoDB Data Capsule will be contained in. Click the yellow `+` button in the bottom left of the screen, select **New Capsule**, then select the **MongoDB** option from the dropdown.

![Create Data Capsule](../../.gitbook/assets/create-mongodb-capsule.png)

In the New Data Capsule dialog, choose **MongoDB Database Cluster** as your data type, then click the **Create Capsule** button.

## Connecting a Data Capsule to a Backend Capsule

To connect a Data Capsule to a Backend Capsule hosted on Code Capsules, you need to provide a database connection string from your Database Capsule to your Backend Capsule. If you're not hosting your backend application on Code Capsules, you can jump to [this section](./#connecting-to-a-mongodb-data-capsule-from-outside-code-capsules), as this step is not applicable in that use case.

Navigate to the Backend Capsule and click **Config** to open the Capsule's config tab. Scroll down to the **Data capsules** section, where your recently created Data Capsule will show.

![Connect Data Capsule](../../.gitbook/assets/bind-mongodb-capsule-env.png)

Click **View** to view the environment variables from the Data Capsule. Click the `+` next to the `Connection string` variable to create a `DATABASE_URL` environmental variable in your Backend Capsule, which gives access to the services and features of your Data Capsule.

We can use this database variable in code to read and write to our Data Capsule. Copy the value of the `DATABASE_URL` variable and append `/your_db_name?authSource=admin` to it as a query parameter. Make sure to replace `your_db_name` with the actual name of your database. This tells the Data Capsule to read and write to the specified database. If a database named `your_db_name` doesn't exist, the Data Capsule will create it. This allows you to have multiple databases in one Data Capsule.

### Connecting to a MongoDB Data Capsule from a Python Application

If your Backend Capsule is a Python application, use the following code to connect to your MongoDB Data Capsule:

```python
import os
import pymongo

data_capsule_url = os.getenv('DATABASE_URL')
database_one_url = data_capsule_url + "/database_one?authSource=admin"
production_database_url = data_capsule_url + "/production_database?authSource=admin"

client = pymongo.MongoClient(database_one_url)
db = client.database_one

### Do something with the db variable here

```

### Connecting to a MongoDB Data Capsule from a Node.js Application

If your Backend Capsule is a Node.js application, use the following code to connect to your MongoDB Data Capsule:

```js

data_capsule_url = process.env.DATABASE_URL
database_one_url = data_capsule_url + "/database_one?authSource=admin"
production_database_url = data_capsule_url + "/production_database?authSource=admin"

var MongoClient = require('mongodb').MongoClient;

MongoClient.connect(database_one_url, function(err, db) {

    // Do something with the db variable here
});

```

## Connecting to a MongoDB Data Capsule from Outside Code Capsules

If you're not hosting your backend application on Code Capsules, you can still connect your Data Capsule to it. The first step is to allow public access to your Data Capsule. Make sure this option is turned on in the **Details** tab of your Data Capsule's details screen.

![Get Connection String](../../.gitbook/assets/connection-string.png)

If public access to your Data Capsule is enabled, a connection string is visible below the **Public Access** switch, as shown in the above picture. Copy this connection string and append `&authSource=admin` to it so that its format is similar to `mongodb://92d79d9b-64f2-0:240e1937-a9fe-4@data-capsule-kykgmd.codecapsules.co.za:27017/app?ssl=true&authSource=admin`.

The string slice preceeding `/app?ssl=true&authSource=admin` will be different in your case since you're using a different Data Capsule from the one used for the purpose of writing this reference guide. The last `/app?ssl=true&authSource=admin` part doesn't need to be replaced though. Paste the appended connection string into your backend application's code to access your Data Capsule's services.

### Connecting to a MongoDB Data Capsule from an Externally Hosted Python Application

If your backend application is written in Python, use the following code to connect to your MongoDB Data Capsule:

```python
import pymongo

database_url = "mongodb://92d79d9b-64f2-0:240e1937-a9fe-4@data-capsule-kykgmd.codecapsules.co.za:27017/app?ssl=true&authSource=admin"

client = pymongo.MongoClient(database_url)
db = client.database_one

### Do something with the db variable here

```

If you copied the code snippet above remember to replace the `mongodb://92d79d9b-64f2-0:240e1937-a9fe-4@data-capsule-kykgmd.codecapsules.co.za:27017` string slice in the `database_url` variable with the corresponding value for your connection string.

### Connecting to a MongoDB Data Capsule from an Externally Hosted Node.js Application

If your backend application is written in Node.js, use the following code to connect to your MongoDB Data Capsule:

```js

database_url = "mongodb://92d79d9b-64f2-0:240e1937-a9fe-4@data-capsule-kykgmd.codecapsules.co.za:27017/app?ssl=true&authSource=admin"

var MongoClient = require('mongodb').MongoClient;

MongoClient.connect(database_url, function(err, db) {

    // Do something with the db variable here
});

```

If you copied the code snippet above remember to replace the `mongodb://92d79d9b-64f2-0:240e1937-a9fe-4@data-capsule-kykgmd.codecapsules.co.za:27017` string slice in the `database_url` variable with the corresponding value for your connection string.
