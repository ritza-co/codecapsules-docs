---
title: Build an Imgur clone with Uppy and Caddy
description: Create a Imgur clone that lets users easily upload images and get a shareable link with Uppy and Caddy.
image: assets/tutorials/build-imgur-clone-uppy-caddy/cover.png
---

# Build an Imgur clone with Uppy and Caddy

![Docker PHP CRUD app cover](../assets/tutorials/build-imgur-clone-uppy-caddy/cover.png)

You probably sometimes have an image or other file locally or in your clipboard and you want to publish it to the internet and get a shareable URL as easily as possible, either to send to a friend or colleague, or to link to from a blog post.

Sites like [Imgur](https://imgur.com) let you do this for free, but they also display a lot of adverts, and you ultimately lose control of what you upload: if you upload something as an anonymous user, you it's hard to delete a file later if you uploaded it as an anonymous user.

In this tutorial, we'll build a basic image uploading site similar to Imgur that lets users upload files and instantly get a public URL, without requiring an account or anything else. You'll be able to host it on a custom domain, so you can share URLs like `i.mycompany.com/123456778.png` instead of `i.imgur.com/epzw0Sc.png`.

We'll use 

- [NodeJS](https://nodejs.org) and [ExpressJS](https://expressjs.com) to build the web application that handles uploads and saving the files on a server
- [Uppy](https://uppy.io) as a frontend file-upload plugin
- [Parcel](https://parceljs.org) as a build tool for our JavaScript application
- [Caddy](https://caddyserver.com) as a web server to efficiently serve the uploaded files
- [Docker](https://www.docker.com) as a container for Caddy to make development and deployment easier

The final app will look like this.

![]()

To follow along, you should have some basic NodeJS and Docker knowledge, and have NodeJS and Docker installed locally. You'll need to install some packages from NodeJS, and run Parcel and Docker to build the application.

## Setting up and creating the frontend

We'll split our application into a few different files with the following structure.

```
image-sharing-app/
    src/
        app.js
        index.html
    server.js
    package.json
    Caddyfile
    Dockerfile
```

Create these files and directories by running the following:

```
mkdir image-sharing-app
cd image-sharing-app
mkdir src
touch src/app.js src/index.html server.js package.json Dockerfile Caddyfile
```

Now we can install the packages we need. Add the following to the `package.json` file.

```json
{
  "dependencies": {
    "@uppy/core": "^3.0.4",
    "@uppy/dashboard": "^3.1.0",
    "@uppy/xhr-upload": "^3.0.3",
    "cors": "^2.8.5",
    "express": "^4.18.2",
    "multer": "^1.4.5-lts.1"
  }
}
```

Now run `npm i` to install the dependencies. Next, run:

```
npm install parcel -g
```

This installs Parcel globally, so we can call it from the command line. We'll use Parcel because Uppy is a big package and we'll only be using small parts of it. Parcel lets us use syntax like `import Uppy from '@uppy/core';` in a vanilla JavaScript file, instead of having to pull in the entire Uppy package from a CDN using syntax like `<script src="https://releases.transloadit.com/uppy/v3.2.2/uppy.min.js"></script>` which would include all of Uppy's plugins and make our app a lot slower to load.

### Building the `index.html` file

Now in `src/index.html, add the following code.

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8"/>
        <title>Image Uploader</title>
        <link
                href="https://releases.transloadit.com/uppy/v3.2.1/uppy.min.css" rel="stylesheet"/>
    </head>
    <body>
        <h1>Easy Image Uploader</h1>
        <div id="drag-drop-area"></div>
        <ul id="links"></ul>
        <script src="app.js" type="module"></script>
    </body>
</html>
```

This is a very minimalistic HTML page that pulls in the CSS needed to style our Uppy upload widget and creates a blank div (`"drag-drop-area"`) that we'll use in our JavaScript file to populate the upload panel.

### Building the `app.js` file

In `src/app.js` add the following code.

```javascript
import Uppy from '@uppy/core';
import XHRUpload from '@uppy/xhr-upload';
import Dashboard from '@uppy/dashboard';

const uploadUrl = `${process.env.HOST_URL}/image`;

const uppy = new Uppy()
    .use(Dashboard, {
        inline: true,
        target: '#drag-drop-area',
    })
    .use(XHRUpload, {
        endpoint: uploadUrl,
        fieldName: 'userFile',
        formData: true,
    })
```

### Buliding the `server.js` file

```javascript
const express = require('express');
const cors = require( 'cors'); 
const path = require('path');
const multer = require('multer');

const app = express();
app.use(express.static('dist'));
app.use(cors());

app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname + "/dist/index.html"));
});

const port = process.env.PORT || 3000;
app.listen(port, () => { 
    console.log("listening on port " + port); 
});
```













