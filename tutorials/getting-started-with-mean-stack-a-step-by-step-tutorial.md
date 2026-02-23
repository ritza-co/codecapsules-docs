---
cover: .gitbook/assets/mean-stack-cover.jpg
coverY: 0
coverHeight: 422
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

# Getting Started With MEAN Stack: A Step-by-Step Tutorial

As a web developer, diving into the world of full-stack development can be both exciting and challenging. One of the popular choices for building robust web applications is the MEAN stack. MEAN stands for MongoDB, Express.js, Angular, and Node.js – a powerful combination of technologies that allows you to create modern, scalable, and efficient web applications.

In this step-by-step tutorial, we will guide you through the process of building a production-ready MEAN stack application. By the end of this tutorial, you will have a solid understanding of how these technologies work together and be ready to start your own MEAN stack project.

### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

Before we begin, ensure that you have the following tools and knowledge:

1. Node.js: Install [Node.js](https://nodejs.org/). Node.js will enable you to run JavaScript on the server-side.
2. npm (Node Package Manager): npm comes bundled with Node.js and is used to manage packages and dependencies for your project.
3. MongoDB: Install [MongoDB](https://www.mongodb.com/). MongoDB is a NoSQL database that stores your application’s data.
4. Angular CLI: Install Angular CLI globally using npm. You can do this by running \`npm install -g @angular/cli\`. Angular is a front-end framework for building dynamic web applications.

Now that you have the necessary tools in place, let’s get started with building your MEAN stack application!

### Set Up Your Project <a href="#set-up-your-project" id="set-up-your-project"></a>

1\. Create a new directory for your project:

```
mkdir mean-stack-app
cd mean-stack-app
```

2\. Initialize your Node.js project:

```
npm init -y
```

### Create the Backend with Express.js and Node.js <a href="#create-the-backend-with-expressjs-and-nodejs" id="create-the-backend-with-expressjs-and-nodejs"></a>

1\. Install Express.js, a popular Node.js web application framework:

```
npm install express --save
```

2\. Create a basic Express.js server in a file named `server.js`. This will be your application’s backend:

```
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
res.send('Hello MEAN Stack!');
});

app.listen(port, () => {
console.log(`Server is running on port ${port}`);
});
```

3\. Start your Node.js server:

```
node server.js
```

Your server should now be running at `http://localhost:3000`, and you should see “Hello MEAN Stack!” when you visit this URL in your browser.

### Create the Frontend with Angular <a href="#create-the-frontend-with-angular" id="create-the-frontend-with-angular"></a>

1\. Generate a new Angular project:

```
ng new mean-stack-frontend
```

Follow the prompts to configure your Angular project.

2\. Navigate to your Angular project directory:

```
cd mean-stack-frontend
```

3\. Start the Angular development server:

```
ng serve
```

Your Angular application will be available at `http://localhost:4200`. Visit this URL in your browser, and you should see the default Angular app.

### Connect Angular to Express.js <a href="#connect-angular-to-expressjs" id="connect-angular-to-expressjs"></a>

1\. Open the `src/environments/environment.ts` file in your Angular project and update the `apiUrl` property to point to your Express.js server:

```
export const environment = {
production: false,
apiUrl: 'http://localhost:3000',
};
```

2\. Create an Angular service to interact with your Express.js backend. You can generate a service using the Angular CLI:

```
ng generate service api
```

Implement your API service to make HTTP requests to your Express.js server.

3\. Update your Angular components to use the API service to fetch data from the server and display it in your application.

### Set Up MongoDB <a href="#set-up-mongodb" id="set-up-mongodb"></a>

1\. Start your MongoDB server:

```
mongodb
```

2\. Connect your Express.js backend to MongoDB using a MongoDB driver such as Mongoose.

3\. Create MongoDB models and routes to handle data storage and retrieval in your Express.js application.

### Deploy Your MEAN Stack Application <a href="#deploy-your-mean-stack-application" id="deploy-your-mean-stack-application"></a>

1\. Choose a hosting provider for your MEAN stack application. Popular options include Heroku, AWS, and Microsoft Azure.

2\. Deploy your Express.js backend and MongoDB to your chosen hosting environment.

3\. Deploy your Angular frontend to a hosting service like Netlify, Vercel, or GitHub Pages.

4\. Update your Angular `environment.ts` file to point to your production API URL.

Congratulations! You’ve now built and deployed a production-ready MEAN stack application. This tutorial provides a solid foundation for creating web applications using the MEAN stack, but there is still much more to explore and learn. As you gain experience, you can enhance your application with features like user authentication, data validation, and more.

Happy coding, and best of luck with your MEAN stack journey!
