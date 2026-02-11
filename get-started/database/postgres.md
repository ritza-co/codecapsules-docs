# Postgres

In this tutorial, we'll create a PostgreSQL Data Capsule to provide persistent storage for your applications hosted on Code Capsules.

## Create a PostgreSQL Data Capsule

Log in to your [Code Capsules](https://codecapsules.io) account and navigate to the Space your PostgreSQL Data Capsule will be contained in. Click the yellow `+` button and select the **PostgreSQL Data Capsule** option from the dropdown.

![Create Data Capsule](../.gitbook/assets/create-postgresql-capsule.png)

Choose your payment option, then click the **Create Capsule** button.

## Connecting a Data Capsule to a Backend Capsule

To use the Data Capsule with your Backend Capsule, you'll need to link the two. Navigate to the Backend Capsule and click **Config** to open the Capsule's configuration tab. Scroll down to the **Data capsules** section and you'll see your recently created Data Capsule.

![Bind Data Capsule](../.gitbook/assets/postgresql-connection-string.png)

Click **View** to view the environment variables from the Data Capsule. Click the `+` next to the `Connection string` variable to create a `DATABASE_URL` environmental variable in your Backend Capsule, which gives access to services and features of your Data Capsule.

We can use this database variable in code to read and write to our Data Capsule. Copy the value of the `DATABASE_URL` variable and append `/your_db_name` to it as a query parameter. Make sure to replace `your_db_name` with the actual name of your database. This tells the Data Capsule to read and write to the specified database. If a database named `your_db_name` doesn't exist, the Data Capsule will create it. This allows you to have multiple databases in one Data Capsule. Take note, if you copy the `DATABASE_URL` value from the Capsule, it will already include the name of the default database you used whilst creating the Data Capsule as part of the string.

### Connecting to a PostgreSQL Data Capsule from a Python Application

If your Backend Capsule is a Python application, use the following code to connect to your PostgreSQL Data Capsule:

```python
import os
import psycopg2

# Get the DATABASE_URL from environment variables
data_capsule_url = os.getenv('DATABASE_URL')

# Connect to the PostgreSQL database
conn = psycopg2.connect(data_capsule_url)

# Create a cursor to interact with the database
cur = conn.cursor()

# Example query (optional)
cur.execute("SELECT version();")
print(cur.fetchone())

# Always close cursor and connection when done
cur.close()
conn.close()
```

### Connecting to a PostgreSQL Data Capsule from a Node.js Application

If your Backend Capsule is a Node.js application, use the following code to connect to your PostgreSQL Data Capsule:

```js
const { Client } = require('pg');

// Get the DATABASE_URL from environment variables
const client = new Client({
  connectionString: process.env.DATABASE_URL
});

// Connect and interact with the database
client.connect()
  .then(() => {
    console.log("Connected!");
    // Example query (optional)
    return client.query('SELECT version();');
  })
  .then(res => {
    console.log(res.rows[0]);
  })
  .catch(err => {
    console.error("Connection error", err.stack);
  })
  .finally(() => {
    client.end();
  });
```

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](../).
