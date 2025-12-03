# Build a MERN Job Board

Like other full-stack applications, a MERN (MongoDB, Express, React, Node.js) stack has the advantage of needing only one capsule to host both the frontend and backend of an application.

In this tutorial, we’ll extend a boilerplate MERN application to make a job board where users can view and submit available jobs.

### Getting Started <a href="#getting-started" id="getting-started"></a>

Head over to the [MERN stack deployment guide](https://app.gitbook.com/s/xjp0G5hHSJs8nyv5Z5g7/full-stack/mern-stack) and follow the steps outlined there to set up the boilerplate application. You will need to clone the forked repository to your local development environment to extend the functionality of the boilerplate application.

Before we can view the application’s frontend, we need to install the `node_modules` for the backend and set a local `DATABASE_URL` environment variable similar to the one on Code Capsules.

Navigate to the project’s root folder in a terminal or command prompt window and run `npm install` there. Reference this [MongoDB setup guide](https://app.gitbook.com/s/xjp0G5hHSJs8nyv5Z5g7/database/mongodb) to ensure that public access is turned on for your data capsule. Copy the value of the connection string and append `&authSource=admin` to it so that its format is similar to `mongodb://09229f61-205e-1:325368d6-3c25-e@data-capsule-ndulvw.codecapsules.co.za:27017/app?ssl=true&authSource=admin`.

Set the local `DATABASE_URL` environment variable by following the steps below:

* Create a `.env` file in the root folder.
* Add the line below to the `.env` file replacing the connection string with your own.

```
DATABASE_URL=mongodb://09229f61-205e-1:325368d6-3c25-e@data-capsule-ndulvw.codecapsules.co.za:27017/app?ssl=true&authSource=admin
```

* Run the command below in a terminal window from the root folder to install the package for loading environment variables.

```
npm install dotenv
```

* Open the `index.js` file in the root folder and add the following line just below the other require statements.

```
require('dotenv').config();
```

### Extending the Frontend <a href="#extending-the-frontend" id="extending-the-frontend"></a>

Open the project’s root folder and navigate to the client directory. This is where you’ll find the code for the React frontend in the `src` subdirectory. Open a terminal and run `npm install` from the client directory to install the `node_modules` required by the frontend code.

Next, type in `npm run build`. This command creates a `build` folder with an optimized version of our frontend source code. This code has all the extra spacing removed, which is great for efficiency but impossible for humans to read or edit. An excerpt is shown below:

```
a=document.createElement("script");a.charset="utf-8",a.timeout=120,i.nc&&a.setAttribute("nonce",i.nc),a.src=function(e){return i.p+"static/js/"+({}[e]||e)+"."+{3:"fe1e148c"}[e]+".chunk.js"}(e);var c=new Error;u=function(r){a.onerror=a.onload=null,clearTimeout(f);var t=o[e];if(0!==t){if(t){var n=r&&("load"===r.type?"missing":r.type),u=r&&r.target&&r.target.src;
```

Whenever we change our application’s frontend code, we edit the files in the `src` directory and use the `npm run build` command to create the optimized code in the `build` directory, which will be executed when we load our application in a web browser.

Run `node index.js` in the terminal from the project’s root folder to start the boilerplate MERN application. You can view it in your browser at `http://localhost:8080`. The app should look something like this:

![mern stack](.gitbook/assets/build-a-mern-job-board/mern-stack.png)

Let’s extend this frontend to reflect the job board functionality.

#### Adding the `SubmitJob` Component

Create a `components` folder within the `client/src` directory (`client/src/components`) to house the submit job and view jobs components that we’re going to build next. Create a `submitJob.js` file in the components folder and add the following code:

```
import React, { useState } from 'react'
import axios from 'axios'

const SubmitJob = () => {

    const [jobTitle, setJobTitle] = useState("")
    const [jobDescription, setJobDescription] = useState("")
    const [jobLocation, setJobLocation] = useState("")
    const [jobSalary, setJobSalary] = useState(null)

    const postJob = (e) => {
        const data = { title: jobTitle, description: jobDescription, location: jobLocation, salary: jobSalary }
        axios.post('/api/jobs/', data)
        .then(response => {
          console.log(response)
        })
    }

    return (
        <div className="submitJobContainer">
            <h3>Submit a Job</h3>
            <form className="formContainer" onSubmit={postJob}>
                <input type="text" name="title" placeholder="Job Title" onChange={e => setJobTitle(e.target.value)} />
                <input type="text" name="description" placeholder="Job Description" onChange={e => setJobDescription(e.target.value)} />
                <input type="text" name="location" placeholder="Job Location" onChange={e => setJobLocation(e.target.value)} />
                <input type="number" name="salary" placeholder="Job Salary" onChange={e => setJobSalary(e.target.value)} />
                <button className="submitButton" type="submit">Submit</button>
            </form>
        </div>
    )
}

export default SubmitJob
```

The `SubmitJob` component uses state to keep track of the job field values as they are entered by a user. You can add more state variables to capture more job fields in your application. When the user clicks “Submit”, the `postJob` method posts the job field values to the endpoint specified in the `axios.post()` method. Notice that we use the relative URL `/api/jobs/` in the `post` request, since the frontend will be hosted at the same URL as the backend.

#### Adding the `ViewJobs` Component

Create a `viewJobs.js` file in the components folder and add the following code to it:

```
import React, { useState, useEffect } from 'react'
import axios from 'axios'

const ViewJobs = () => {

    const [jobsStateArray, setJobsStateArray] = useState([])

    useEffect(() => {          
        axios.get('/api/jobs/')
        .then(response => {
            console.log(response)
            setJobsStateArray(response.data)
          })
    }, [])

    return (
        <div className="viewJobsContainer">
            <h3>View Available Jobs</h3>
            {jobsStateArray.map((item, index) => {
                return (
                    <div className="jobCard">
                        <p><strong>Job Title</strong>: {item.title}</p>
                        <p><strong>Location</strong>: {item.location}</p>
                        <p><strong>Description</strong>: {item.description}</p>
                        <p><strong>Salary</strong>: {item.salary}</p>
                    </div>
                )
            })}
        </div>
    )
}

export default ViewJobs
```

The `ViewJobs` component uses the `useEffect` hook to fetch available jobs as soon as the page loads. After fetching the jobs, they are stored in the `jobsStateArray` before being displayed using the `map` function.

#### Viewing the Frontend

We need to import the two components we have just created in `src/App.js` before we can see the changes we just made. Open `App.js` and replace its contents with this code:

```
import logo from './logo.svg';
import './App.css';
import React, { useState } from 'react';
import axios from 'axios';
import SubmitJob from './components/submitJob';
import ViewJobs from './components/viewJobs';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <h3>Job Board</h3>
      </header>
      <SubmitJob />
      <ViewJobs />
    </div>
  );
}

export default App;
```

Here, we’re importing the `SubmitJob` and `ViewJobs` components so that they can be rendered when React displays the contents of `App.js`.

Now you can run `npm run build` again from the client directory to build the app, then open your browser to take a look at the extended frontend.

You’ll notice the page layout has changed, but the styling is a bit off. Add the contents of [this CSS file](https://github.com/ritza-co/mern-job-board/blob/main/client/src/App.css) to `src/App.css` to make our frontend prettier.

When you rebuild your application and take a look at the frontend, it should look like this:

![job board ui](.gitbook/assets/build-a-mern-job-board/job-board-ui.png)

### Extending the Backend <a href="#extending-the-backend" id="extending-the-backend"></a>

We’re now ready to extend the backend to include functionality for the addition and retrieval of jobs on the job board.

#### Adding the Job Model

Let’s define our job model to declare which job fields we want to save. Open `app/models/index.js` and replace its contents with this code:

```
const dbConfig = require("../config/db-config.js");
const mongoose = require("mongoose");
mongoose.Promise = global.Promise;

const db = {};
db.mongoose = mongoose;
db.url = dbConfig.url;
db.jobs = require("./job.model.js")(mongoose);

module.exports = db;
```

Here, we create and export a `db` variable that we’ll use to access the database. We’ll use the [Mongoose library](https://mongoosejs.com/) to handle all communication with our MongoDB database.

Now create a file named `job.model.js` in the `models` folder to define the fields and field types of the job model. Populate it with the code below. If you added more fields to your frontend `SubmitJob` component, remember to add them here too, or they won’t be saved when a user submits a job.

```
module.exports = mongoose => {
    var schema = mongoose.Schema(
        {
        title: String,
        description: String,
        location: String,
        salary: Number
        },
        { timestamps: true }
    );

    schema.method("toJSON", function() {
        const { __v, _id, ...object } = this.toObject();
        object.id = _id;
        return object;
    });

    const Job = mongoose.model("job", schema);
    return Job;
};
```

By default, Mongoose adds an `_id` property to `ObjectId` fields. The `schema.method()` function makes sure the name of the `ObjectId` field is just `id`, which is the name our frontend expects.

Delete the `person.model.js` file that came with the boilerplate project, as we won’t be needing it.

#### Adding Job Controllers

The next step is to create controllers to decide whether the app is reading or writing jobs to the database. In the `app/controllers/` folder, create a file named `job.controller.js` and add the following code to it:

```
const db = require("../models");
const Job = db.jobs;

// Create and Save a new Job
exports.create = (req, res) => {
  // Validate request
  if (!req.body.title) {
    res.status(400).send({ message: "Content can not be empty!" });
    return;
  }

  // Create a Job
  const job = new Job({
    title: req.body.title,
    description: req.body.description,
    location: req.body.location,
    salary: req.body.salary
  });

  // Save Job in the database
  job
    .save(job)
    .then(data => {
      res.send(data);
    })
    .catch(err => {
      res.status(500).send({
        message:
          err.message || "Some error occurred while creating the Job."
      });
    });
};

// Retrieve all Jobs from the database.
exports.findAll = (req, res) => {
    const title = req.query.title;
    var condition = title ? { title: { $regex: new RegExp(title), $options: "i" } } : {};

    Job.find(condition)
      .then(data => {
        res.send(data);
      })
      .catch(err => {
        res.status(500).send({
          message:
            err.message || "Some error occurred while retrieving Jobs."
        });
      });
};
```

The `create` export is responsible for creating a new job object using the job model and saving it to the database. The `findAll` export retrieves all jobs that were previously submitted.

Delete the `person.controller.js` file that came with the boilerplate project, as we won’t be needing it.

#### Adding Endpoints

The last step in extending the backend is to add endpoints for the frontend to make `post` and `get` requests to. Create a `job.routes.js` file in the `app/routes/` folder and add the following code:

```
module.exports = app => {
    const jobs = require("../controllers/job.controller.js");

    var router = require("express").Router();

    // Create a new Job
    router.post("/", jobs.create);

    // Retrieve all Jobs
    router.get("/", jobs.findAll);

    app.use('/api/jobs', router);
};
```

The routes use request methods and the controller exports we made earlier to decide what happens when each endpoint is hit by a `get` or `post` request.

Delete the `person.routes.js` file that was in the routes folder.

In `index.js` in the root folder of the project, find the following line:

```
require("./app/routes/person.routes")(app);
```

and change it to:

```
require("./app/routes/job.routes")(app);
```

This tells our backend to use the routes defined in our `job.routes.js` file.

### Integrating the Frontend and Backend <a href="#integrating-the-frontend-and-backend" id="integrating-the-frontend-and-backend"></a>

Our Express backend uses the contents of the `client/build` folder to render the frontend of our MERN stack application. The lines below in the `index.js` file in the root folder handle that responsibility:

```
const path = __dirname + '/client/build/';
const app = express();
app.use(express.static(path));
```

### Using Version Control <a href="#using-version-control" id="using-version-control"></a>

We’ll use version control to keep track of the new files we added when we were extending our application.

We don’t want to track the build folder in git (the frontend will rebuild when we deploy to Code Capsules), so we’ll add this folder to be ignored in the `.gitignore` file in the project’s root folder, like this:

```
/client/build
```

#### Git Add

To add all the new files we created, run the command below in the root folder of the project.

```
git add -A
```

#### Git Commit

Next, we need to commit the files we just added to our repository. To do this, run this command:

```
git commit -m "Added job board files"
```

#### Git Push

The final step is to push our committed changes to the remote repository which Code Capsules is linked to. Run the command below to push the changes we just made:

```
git push origin main
```

Code Capsules automatically deploys the new version of your application as soon as you push to the deploy branch, which is `main` in this case.

That’s it, your job board should be fully functional now.
