---
cover: .gitbook/assets/heroku-emigration-cover.jpg
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
---

# How To Migrate from Heroku to Code Capsules

This guide demonstrates how to move a suite of applications running on Heroku, the AI platform as a service (AI PaaS), to Code Capsules. It walks you through the Code Capsules equivalents of Heroku components, how to change your connection settings to work on Code Capsules, how to export and restore your database, and the common pitfalls to avoid.

[Heroku is no longer adding new features](https://www.theregister.com/2026/02/09/heroku_freeze) as its parent company focuses on AI business opportunities. If you're considering moving your Heroku app to another PaaS, consider the [benefits of Code Capsules](https://www.codecapsules.io/compare).

- [How To Migrate from Heroku to Code Capsules](#how-to-migrate-from-heroku-to-code-capsules)
  - [Document Your Heroku System](#document-your-heroku-system)
  - [An Example Full-Stack Application](#an-example-full-stack-application)
  - [Reviewing the App on Heroku](#reviewing-the-app-on-heroku)
    - [The Frontend](#the-frontend)
    - [The Backend](#the-backend)
    - [The Cron Job](#the-cron-job)
    - [Summary of the System](#summary-of-the-system)
  - [Creating the App on Code Capsules](#creating-the-app-on-code-capsules)
    - [The Database](#the-database)
    - [The Backend and Frontend](#the-backend-and-frontend)
    - [The Cron Job](#the-cron-job-1)
  - [Other Components and Cross-Cutting Concerns in Code Capsules](#other-components-and-cross-cutting-concerns-in-code-capsules)
    - [Scaling Capsules](#scaling-capsules)
    - [Running Multiple Applications in a Capsule](#running-multiple-applications-in-a-capsule)
    - [Ensuring Data Privacy](#ensuring-data-privacy)
  - [Next Steps](#next-steps)

## Document Your Heroku System

Before moving your app, you first need to document all of its components and settings:

- Using either the Heroku CLI or web interface, list all the apps you want to migrate.
- Check each app's **Resources** tab (or `Procfile` in your repository) to see whether the dyno is `web`, which has a public URL, or `worker`, which is for backend tasks only. Note whether the dyno is a standard framework (like Python) or a custom Docker image, and note how much CPU, RAM, and disk space it uses.
- List all addons for each app, like databases and schedulers, and their connection settings.
- Note the exact URLs, domains, and subdomains of each app and addon.
- Copy the settings (config vars) into a spreadsheet.
- Examine the deployment process of each app. To migrate an app to Code Capsules, you need to deploy the app using a corporate Git host. Organize your repositories so that each app and version (such as QA and production) is in its own branch, or subfolder in a branch. This allows you to deploy each separately.

If your database is large, it may be the most complicated component to migrate. The simplest solution is to take your entire system offline, so that no new data is added. Then, back up your database from Heroku and restore it to your new host. 

If you don't want to take your app offline, you need to use the dual-write method, which works roughly as follows:

- Create a database on your new host. Deploy your applications to the new host as well.
- Back up and restore static tables (enumerations) to the new database. These tables won't change, so you can migrate them in advance.
- Point your new application to the database on the old host, but write new data to both databases — new and old. This allows you to migrate gradually to the new host. Eventually you'll be able to point all applications to the database on the new host and take the old database offline.

There are a lot of edge cases and reconciliation problems with dual-writing. Consult a dedicated guide for more details.

## An Example Full-Stack Application

Let's look at the most common type of application hosted on Heroku: a full-stack web application.

Full-stack web apps need a system comprised of the following components:

![System design](.gitbook/assets/app.svg)

- A static web server to serve the website (or a dynamic host in something like PHP or Node.js)
- A backend web service that the website talks to
- A database, where the web service stores user data
- Another backend service, such as a cron job (scheduled task) that processes database data, an analytics service, or an email server

In Heroku, each of these components runs in a separate "dyno" (container).

Other common components include:

- Object storage, for storing images
- QA versions of the live components
- A message queue, like RabbitMQ
- An in-memory cache, like Redis

## Reviewing the App on Heroku

Before moving our example app to Code Capsules, let's consider how we'd set it up on Heroku. By understanding Heroku's peculiarities, we can better plan our migration to another provider.

### The Frontend

On Heroku, you can create an app without assigning any dyno resources and without deploying code. That app then acts as a placeholder. Heroku creates and runs the dyno after you deploy your code, via a [`Procfile` file](https://devcenter.heroku.com/articles/procfile). This file might have an instruction like `web: node server.ts`, which tells Heroku to run a new web dyno.

Let's say that all the example app code is in TypeScript and Node.js (although Heroku does support equivalent features in other languages to the ones we discuss in this guide).

Heroku doesn't support static websites. Previously, you could use a custom buildpack, `heroku-buildpack-static`, to add a static website, but it has been deprecated. Instead, the example frontend dyno uses a Node.js static server: `http-server`.

Heroku has multiple ways to deploy code to a dyno. Identify which method your app uses:

- **[API (HTTP calls)](https://devcenter.heroku.com/articles/build-and-release-using-the-api):** This is the simplest solution. Just zip your code and use `curl` to send the file to Heroku.
- **CLI:** By downloading and running the Heroku CLI in your terminal, you can deploy your app to Heroku in three ways:
  1. By zipping and pushing a build (this is the same as the API)
  2. By using the CLI to add Heroku as a Git remote server to your app's local Git repository, you can `git push` to that remote and Heroku will deploy it.
  3. By building your own Docker image and pushing it to the Heroku Registry. You lose the PaaS benefits of automated security patching and runtime optimization.
- **GitHub:** Set Heroku to pull from your repository when you push to GitHub. (GitLab, Bitbucket, and Codeberg are not supported.)

For the frontend to work correctly, you need to set a backend URL, so the website knows where to talk to the web service. In Heroku, there are two ways to do this:

1. You can set the values you need as [config vars](https://devcenter.heroku.com/articles/config-vars) in your dyno's **Settings** tab. The values will appear as environment variables at runtime. For example, in Node.js you could then access `process.env.DATABASE_URL`.
2. You can add the settings you need to a `.env` file along with your deployed code, and access them in the same way you would access a Heroku config var.

If you need to change a variable in any way before using it, you can add a postbuild step in your `package.json` file. For example, the following package file copies the example backend URL to a JavaScript file that is included directly in the example app's static HTML page:

```js
"scripts": {
    "heroku-postbuild": ". ./.env && echo \"const backendUrl = '${BACKEND_URL}';\" > config.js",
    "start": "http-server . -p $PORT"
  },
```

### The Backend

To ensure you don't get CORS errors when the backend and frontend components talk to each other, you must add the frontend app URL as a setting for the backend. Note that you need to deploy **both** the frontend and the backend to get both their URLs and set both environment variables before the system will work. You cannot deploy one component completely before the other.

The main difference with the backend is that it has access to the database. In Heroku, a database is not managed as an independent application. Instead, a database is an "addon" to the backend app; although you can give other apps access to the database if necessary. You can create a PostgreSQL database in Heroku by configuring it as an addon in the **Resources** tab of an app.

Heroku automatically saves the database connection URL as a config var in the associated app, so your code can use the connection details as an environment variable.

Since our example application has only a single `user` table with only two columns, we created it in the backend code, without needing to run SQL scripts.

If you need to run database scripts or query data, Heroku exposes your database publicly. You can use a command such as the following to connect to the database: 

```sh
psql "$(curl -s https://api.heroku.com/apps/emigrate-backend/config-vars -H "Authorization: Bearer $KEY" -H "Accept: application/vnd.heroku+json; version=3"| jq -r '.DATABASE_URL')" -c 'SELECT * FROM "user"'

username | password
----------+----------
 t@t.com  | t

(1 row)
```

This command shows that our backend successfully saved the user's email address and plaintext password when they registered on the site.

### The Cron Job

Deploying a cron job is almost identical to deploying the backend in Heroku, with three differences:

- You need to specify the dyno type as a `worker` not a `web`.
- You need to attach the app to the existing database addon, instead of creating a new one.
- You need to add a scheduler addon to the app.

The scheduler addon is like a cron manager — it specifies when your app should run. Other than these times, the app isn't running, saving money. For our example scheduler, we set it to run the command `node job.ts` every hour. The script this command runs connects to the database and prints every user — to mimic sending a welcome email or extracting analytics information.

You can see what that looks like below:

![App emailer](.gitbook/assets/appEmailer.webp)

### Summary of the System

Our example Heroku system consists of three apps, shown below, with one database addon and one scheduler addon.

![App components](.gitbook/assets/appComponents.webp)

## Creating the App on Code Capsules

In this section we demonstrate how to deploy the example app (and data) we created in Heroku on Code Capsules.

First, create a Code Capsules [Space](https://docs.codecapsules.io/platform/platform).

Rather than using Heroku's way of grouping applications by a globally unique name, Code Capsules allows you to group all your application components in a Space. We named our Space `emigrate`.

![Code Capsules Space](.gitbook/assets/ccSpace.webp)

From your new Space, click the yellow `+` icon at the bottom left of the screen to create a new Capsule. Select a [Capsule type](https://docs.codecapsules.io/frontend) and follow the prompts provided.

![Code Capsules new capsule](.gitbook/assets/ccNewCapsule.webp)

In the following table, we map each of the four example Heroku components to an equivalent Capsule type in Code Capsules:

Component | Heroku | Code Capsules
---|---|---
emigrate-db | An addon attached to emigrate-backend | PostgreSQL Capsule
emigrate-backend  | Node.js Dyno | Backend Node.js Capsule
emigrate-frontend | Node.js Dyno | Frontend Node.js or static site Capsule
emigrate-emailer | Node.js worker Dyno, using Heroku Scheduler addon | Backend Node.js Capsule with internal scheduling

If you don't see the appropriate Capsule type at first, search for it by name or use the hidden scrollbar to find it in the dropdown.

![Code Capsules new capsule scrolled](.gitbook/assets/ccNewCapsuleDown.webp)

### The Database

Start by deploying the component without any dependencies: the database. Unlike in Heroku, databases are their own entities in Code Capsules.

For the example app, your PostgreSQL database Capsule should look similar to the following:

![Code Capsules database setup](.gitbook/assets/ccDbSetup.webp)

Code Capsules doesn't provide an automated way to import or restore data. Instead, it provides connection details for you to connect to the database directly from your computer so that you can query data.

For the example app, we need to export a database backup from the Heroku database and use the terminal to import it to Code Capsules.

The easiest way to export your database from Heroku is to [use the CLI to create a database backup](https://devcenter.heroku.com/articles/heroku-postgres-backups) and save it to Amazon Simple Storage Service (S3) as a downloadable file.

If your database is only a few MB in size, however, you can connect to it directly and back it up to your local machine. Use the following command to create a backup file called `backup.dump`:

```sh
pg_dump -Fc --no-acl --no-owner "$(curl -s https://api.heroku.com/apps/emigrate-backend/config-vars -H "Authorization: Bearer $KEY" -H "Accept: application/vnd.heroku+json; version=3" | jq -r '.DATABASE_URL')" -f backup.dump
```

Because Code Capsules doesn't create public URLs for database Capsules, you need to use the [Code Capsules CLI](https://docs.codecapsules.io/cli/readme/getting-started/quick-start) to connect to your PostgreSQL Capsule.

**Note:** While you don't have to install the CLI permanently on your machine, you do need Node.js installed on your machine (or to be able to run Node.js through Docker).

Use the following command to run Node.js in a Docker container (to separate your app folder and the rest of your machine from malicious npm extensions), log in to Code Capsules, create a proxy connection to the PostgreSQL database, and restore the database. Replace the login email address and the database Capsule's connection string details (starting from the `npx` command) with your own credentials:

```sh
# start node in docker
docker run --init  -it --rm --platform linux/amd64 --name "cc" -p 5432:5432 -v ".:/app" -w "/app" node:24.12.0-slim bash

# in the docker container terminal:

# install dependencies
apt update && apt install -y libsecret-1-0 dbus dbus-x11 gnome-keyring xsel
dbus-uuidgen > /var/lib/dbus/machine-id

# log in to code capsules
eval $(dbus-launch --sh-syntax) && echo "" | gnome-keyring-daemon --unlock --components=secrets && npx @codecapsules/cli login -e richard@ritza.co

# create a proxy connection to the database
npx @codecapsules/cli proxy capsule -s emigrate-cgkq -c 3861e1ca-0741-f73d-9135-d950af76e42e -P 5432

# in a new terminal on your host machine, restore the database backup
pg_restore --no-acl --no-owner -d "postgresql://postgres:b41eb84b-6698-9@localhost:5432/app" backup.dump

# check if data exists
psql "postgresql://postgres:b41eb84b-6698-9@localhost:5432/app" -c 'SELECT * FROM "user"'
```

Do not press `Q` to copy the connection string from the container as the CLI suggests, because it causes the CLI to crash. Instead, run the last two commands to use the URL from the Capsule website details locally.

If the commands return an error, it may be due to intermittent network problems. Confirm that your settings are correct, then retry until the database proxy initializes correctly.

![Code Capsules CLI](.gitbook/assets/ccCli.webp)

Once your database is restored, close the Docker container; you won't need it again unless you want to query the database.

### The Backend and Frontend

In Heroku, we had to use a Node.js dyno for the frontend instead of a static site. Fortunately, Code Capsules offers static site frontend Capsules that we can use for the example frontend. A static site Capsule is much simpler for static web assets. However, if you run something like a Next.js frontend with server-side rendering, you need a dynamic frontend Capsule.

Frontend and backend Capsules look similar. There are only two differences:

- **Pricing:** A frontend Capsule's minimum configurable CPU resources (and therefore cost) are lower than a backend Capsule.
- **Internal networking:** A backend Capsule can access a database Capsule directly, while a frontend Capsule can't, and must connect to the database via a backend web server instead.

If you don't need a large Capsule, you can set a custom size cheaper than the smallest standard size by expanding the custom radio button at the bottom of the form to see more options:

![Code Capsules Capsule size](.gitbook/assets/ccCapsuleCheap.webp)

While Heroku provides five ways to deploy code, Code Capsules offers only one: Pushing code to a publicly available Git account. Unlike Heroku, Code Capsules supports more Git servers than just GitHub; you can also use GitLab or Bitbucket.

This guide uses GitHub to deploy code to Code Capsules. If you are concerned about data privacy on the available Git servers, refer to our note on [Ensuring Data Privacy](#ensuring-data-privacy).

Deploy the frontend as follows:

1. After choosing a frontend Capsule (in this case, a static site Capsule), link the Code Capsules addon to your GitHub Organization:

    ![Code Capsules Install GitHub addon](.gitbook/assets/ccInstallGithub.webp)

2. In GitHub, grant Code Capsules access to all the repositories that make up your Heroku system:

    ![Code Capsules Install GitHub addon](.gitbook/assets/ccInstallGithubNew.webp)

    If someone else in your organization has already linked Code Capsules to your Git provider, Code Capsules returns an error.

    ![Code Capsules GitHub error](.gitbook/assets/ccGithubError.webp)

    In this case, the original user needs to authorize the repository and then share it in Code Capsules itself. This process is not documented, so you might need to speak to Code Capsules support in Slack for guidance.

    **Note:** Unlike Heroku, you cannot create a Capsule without linking it to a repository, so ensure your code is ready to deploy before setting up your infrastructure.

3. After choosing a repository, specify which folder Code Capsules should deploy.

    Since we had three components in different folders, we chose the `/frontend` folder. If you have one application per repository, you can just leave Code Capsules to use the `/` root folder.

    ![Code Capsules Capsule path](.gitbook/assets/ccCapsulePath.webp)

4. Finally, if you need one, enter a command to run when starting your Capsule. Like Heroku, you don't have to include a dependency installation, like `npm install`, because Code Capsules does that automatically before running your start command.

    You don't need a run command for static sites, so we didn't add one for the example frontend Capsule:

    ![Code Capsules Capsule command](.gitbook/assets/ccCapsuleCommand.webp)

Code Capsules automatically detects your framework, so unlike Heroku, you don't need to use something like Procfiles to specify which framework to run your code with.

Follow the same process to deploy your backend code, only ensure that you use a backend Capsule type (such as the backend Node.js Capsule this guide uses) and that you specify a run command:

1. Ensure that the Code Capsules addon (and therefore your backend Capsule) can access your backend repository in GitHub.

2. Specify which folder you want the addon to deploy to your Capsule.

3. Enter a run command for starting the Capsule.

4. Open the logs to check that your server is running.

At first, our example backend didn't run because the default Node.js version for Code Capsules is maintenance version 20, which doesn't support TypeScript files. TypeScript files require the long-term-support version 24.

```sh
2026-02-18T15:19:50.617
Node.js v20.20.0

2026-02-18T15:18:38.324
TypeError [ERR_UNKNOWN_FILE_EXTENSION]: Unknown file extension ".ts" for /workspace/git-source/backend/server.ts
```

If you encounter a similar issue, update the required Node.js engine at the bottom of your `package.json` file. Be sure to specify the equivalent for your framework, like Python or PHP.

```js
{
  "name": "heroku-app-backend",
  "version": "1.0.0",
  "type": "module",
  "dependencies": {
    "express": "^4.21.0",
    "pg": "^8.13.0"
  },
  "engines": {"node": "24.x"}
}
```

When you redeploy, the Capsule should use the correct Node.js version and run your backend successfully.

Next, you need to set environment variables. The process is similar to Heroku's. Instead of using config vars, Code Capsules has a **Config** tab with a list of environment variables.

1. Open your backend Capsule and navigate to the **Config** tab.

2. To add a connection to the database, click to **View** your database Capsule at the bottom left of the screen.

    ![Code Capsules Capsule config](.gitbook/assets/ccCapsuleConfig.webp)

3. In the **Variables** dialog that appears, click the `+` icon next to a variable to add that value as an environment variable.

    ![Code Capsules Capsule config values from another capsule](.gitbook/assets/ccCapsuleConfig2.webp)

    You can edit the variable name to match whatever you have in your code already, for example, `DATABASE_URL`.

4. When you've added all the variables, click **Save** to the right of the **Environment Variables** header and restart the Capsule.

Like in Heroku, you need to deploy both the frontend app and the backend app before you can get their URLs. Then, set both URLs as environment variables, so they can call each other.

**Note:** You may need to prefix the URLs with `https://`, to ensure your apps run without error.

Static sites cannot access environment variables at runtime, so in your build process, you must include a way to inject the backend URL into the JavaScript of the website.

Finally, delete the Heroku `Procfile` files from your repository, as you won't need them in Code Capsules.

### The Cron Job

Unlike Heroku, Code Capsules doesn't support scheduled tasks or cron jobs. Instead, you need to deploy your code as a backend Capsule that is always running and manages its own scheduling.

For our Node.js example, we could use `node-cron` or JavaScript's native `setInterval()`. We chose `node-cron` and added it as a dependency to create the following makeshift scheduler code:

```js
async function emailJob() {
  const { rows } = await pool.query(`SELECT username FROM "user"`);
  for (const row of rows) console.log(`Emailing ${row.username}`);
}

cron.schedule("0 * * * *", emailJob);
```

![Code Capsules emailer](.gitbook/assets/ccEmailer.webp)

There are some dangers in managing scheduling internally.

- **Memory persistence:** If your Capsule restarts during a task, the schedule is lost until the process starts again.
- **Scaling:** If you scale your app to more than one Capsule, the schedule runs on every container simultaneously, duplicating the work. To prevent this, you need a distributed lock (like Redis).
- **Missed executions:** If the Capsule is down at the exact second a job is scheduled, the scheduler won't run missed jobs once it comes back online.

Another scheduling option is to use an external cron service (like [cron-job.org](https://cron-job.org)), and have it call an HTTP endpoint that you add to your emailer (or backend).

## Other Components and Cross-Cutting Concerns in Code Capsules

This guide covers how to move common system components to Code Capsules, but you might have a few other components.

- **Object storage (file storage):** Object storage, like Amazon S3, is used for storing binary files like photos, for which databases are not suitable. Code Capsules has a file storage Capsule type. Like the database Capsule, the storage Capsule exposes an environment variable for other backend Capsules to use to access it. The variable is just a file path, like `/mnt/storage`, which other Capsules can write to like a normal file system.
- **Domains:** Each Capsule exposes a URL in the **Domains** tab. You can use it to create a CNAME record with your DNS provider, so you can use your company's domain name.
- **QA environments:** While Heroku provides [pipelines](https://devcenter.heroku.com/articles/pipelines) for automatically provisioning testing and production versions of dynos, Code Capsules offers no such thing. To create a test environment for a Capsule, you need to manually create a new Capsule that points to a specific Git branch in your repository, like `/qa`.
- **Blue/green deployments:** A blue/green deployment is a technique used to achieve zero service downtime. Instead of deploying a new code version to your live server, you deploy to another server and then instantly rotate the live URL to the new deployment. This is not possible in Code Capsules. Instead, there will be a delay and your app will be offline while your code is deployed to your live Capsule and the Capsule restarts, downloads dependencies, and runs your start command.
- **CLI automation:** Heroku provides an API and CLI that allow you to write scripts to automate creating, editing, scaling, and deleting dynos. Code Capsules does not. All Capsule creation and management must be done by hand in the web interface. The only alternative is to use an AI agent, like Amp or Claude, to interact with the website on your behalf.
- **Monitoring, logs, and dashboards:** Code Capsules has tabs for this for each Capsule. There is no higher-level system dashboard for the whole Space though.
- **Backups:** You can configure a Code Capsules database to automatically back up your database regularly, and you can create manual backups at any time. Backups can be restored with one click.
- **Message queues and caches:** You can deploy components like RabbitMQ and Redis in their own backend Capsules, which other Capsules can talk to through HTTP. There is a dedicated Redis Capsule type.
- **Custom Docker containers:** If your Heroku app uses a framework that Code Capsules doesn't support, like .NET or Ruby, you can deploy a custom Docker image.

  To do this, create a Capsule using the Docker type. Point the Capsule to a repository that has your code and a Dockerfile that specifies how to build a Docker image to run your code.
  
  For example, the following Dockerfile runs code in the Code Capsules [Python Flask tutorial](https://docs.codecapsules.io/backend/python/flask):

  ```dockerfile
  # syntax=docker/dockerfile:1

  FROM python:3.8-slim-buster

  WORKDIR /python-docker

  COPY requirements.txt requirements.txt
  RUN pip3 install -r requirements.txt

  COPY . .

  CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0"]
  ```

  Code Capsules supports only Dockerfiles, and not Docker compose files or prebuilt Docker images. However, you can prebuild a Docker image on your local computer, upload it to DockerHub, and point your Dockerfile to that image, without needing any other build steps.

### Scaling Capsules

There are multiple ways to scale an application in Code Capsules:

- By manually increasing the RAM, CPU, or disk space of a Capsule.
- By increasing the number of instances (Capsule replicas) to a maximum of three instances.

Code Capsules does not provide the capability to automatically scale your Capsules under load.

### Running Multiple Applications in a Capsule

If you want to run two different services in one Capsule, like a PHP and Ruby server, you need to use a custom Dockerfile that includes both frameworks.

### Ensuring Data Privacy

The three Git servers Code Capsules supports (GitHub, GitLab, and Bitbucket) are domiciled in the US and subject to the US CLOUD Act, which allows the government to request customers' data. Code Capsules does not support an EU-centric solution like Codeberg for deployment. Code Capsules has to link its addon to your Git account, so you can't use a self-hosted GitLab installation either.

Although ultimately there is no guarantee your proprietary code will remain private on a corporate Git server, you can still keep your customer data safe. If you don't trust AWS as a US company, Code Capsules allows you to host Capsules on a European infrastructure provider, or even on your own server on your premises. [Contact a support agent](https://www.codecapsules.io/contact) to configure custom infrastructure for your account. You can't choose infrastructure other than AWS by yourself.

## Next Steps

Interested in trying Code Capsules? [Create a free account](https://app.codecapsules.io/) to take a look around, or ask us anything on [Slack](https://codecapsules.slack.com/join/shared_invite/zt-krsv5ott-_WR~S44xGmjATdpMsRC7yg#/shared-invite/email).
