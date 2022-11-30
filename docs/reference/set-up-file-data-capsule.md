---
title: Set Up Persistent File Data Storage
description: Use a file system directly from your PaaS app for cases where you do not want a full-blown database.
---

# How to Set Up a Persistent File Data Capsule

You need persistent storage to develop your application that solves a real-world problem, but you don't want a full-blown database. In this tutorial, we'll show you how to create a persistent file Data Capsule that you can use with your backend applications running on Code Capsules. 

## Create a File Data Capsule

Log in to your Code Capsules account and navigate to the Space your file data capsule will be contained in. Click yellow `+` button on the top right of the screen and select the "Persistent storage" option from the "Create New Capsule" screen. 

![Create Data Capsule](../assets/deployment/shared/create-persistent-capsule.png)

Choose your payment option, then click the "Create Capsule" button. 


## Binding a Data Capsule to a Backend Capsule

You need to bind the Data Capsule to a Backend Capsule hosted on Code Capsules before you can connect to it and use it.

Navigate to the Backend Capsule and click "Config" to open the capsule's config tab. Scroll down to the "Bind Data capsule" section where your recently created data capsule will show.

![Bind Data Capsule](../assets/deployment/shared/bind-persistent.png)

Click "Bind" to bind your Data and Backend Capsules. During the bind process, Code Capsules creates a `PERSISTENT_STORAGE_DIR` environment variable to let your Backend Capsule know where your Data Capsule resides in order to access its features. Once the two capsules have been bound, you can scroll to the top of the Configure tab to find the value of this variable. 

![PERSISTENT STORAGE DIR Environment Variable](../assets/deployment/shared/env-variables-persistent-storage.png)

The next step is to use this environment variable in code in order to read and write to our Data Capsule. Copy the value of the `PERSISTENT_STORAGE_DIR` variable and paste it in your code as the value of the `db_directory` variable. Alternatively, reference it directly in your code using `os.getenv` for Python or `process.env` for Node.js. 

### Connecting to a File Data Capsule From a Python Application 

If your backend capsule is a Python application, use the following code to connect to your file data capsule:

```python
import os

db_directory = os.getenv('PERSISTENT_STORAGE_DIR')

### Do something with the db_directory variable here
file_to_write = os.path.join(db_directory, "test.txt")

file1 = open(file_to_write, "w")
file1.write("File writing test")
file1.close()

```

### Connecting to a File Data Capsule From a Node.js Application 

If your backend capsule is a Node.js application, use the following code to connect to your file data capsule:

```js

db_directory = process.env.PERSISTENT_STORAGE_DIR
const fs = require('fs')

const content = 'Some content!'

// Do something with the db_directory variable here

fs.writeFile(db_directory + '/test.txt', content, err => {
  if (err) {
    console.error(err)
    return
  }
  //file written successfully
})

``` 
