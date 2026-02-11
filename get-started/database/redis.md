# Redis

In this tutorial, we’ll create a Redis Data Capsule to provide low-latency, in-memory data storage for your application hosted on Code Capsules.

## Create a Redis Data Capsule

Log in to your [Code Capsules](https://codecapsules.io/) account and navigate to the Space your Redis Capsule will be contained in. Click the yellow `+` button in the bottom left of the screen, select **New Capsule**, then select **Redis** from the dropdown.

![Create Data Capsule](../.gitbook/assets/create-redis-capsule.png)

Choose a payment option, and click **Create Capsule**.

## Connecting a Data Capsule to a Backend Capsule

Now we need to connect our Data Capsule to a Backend Capsule using a database connection URL.

Navigate to your Backend Capsule and click on the **Configure** tab. Scroll down to the **Data capsules** section and click **View**. Click the `+` next to the `Connection string` variable to create a `DATABASE_URL` environmental variable in your Backend Capsule, which gives access to services and features of your Data Capsule.

We'll use this environment variable in our app to connect to the Redis database.

![Redis Url](../.gitbook/assets/redis-bind-env.png)

## Connecting to a Redis Data Capsule from a Python Application

If your Backend Capsule is a Python application, use the following code to connect to your Redis database:

```python
import os
import redis

redis_url = os.getenv('REDIS_URL')

connection = redis.from_url(redis_url)

# Do something here
```

## Connecting to a Redis Data Capsule from a Node.js Application

If your Backend Capsule is a Node.js application, use the following code to connect to your Redis database:

```js
let redis = require('redis');
let redis_url = process.env.REDIS_URL

let connection = redis.createClient({
  url: redis_url
});

connection.connect();

// Do something here 

connection.quit();
```

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](../).
