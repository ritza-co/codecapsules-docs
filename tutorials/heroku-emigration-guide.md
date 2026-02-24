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

# How to Emigrate from Heroku to Code Capsules

This guide demonstrates how to move a suite of applications running from the Heroku platform as a service (PaaS) to Code Capsules. You'll learn the Code Capsules equivalents for Heroku components, what changes to make to your connection settings to work on Code Capsules, how to export and restore your database, and common pitfalls to avoid.

[Heroku is no longer adding new features](https://www.theregister.com/2026/02/09/heroku_freeze) as their parent company focuses on AI business opportunities. If you're considering moving your Heroku app to another platform as a service (PaaS), consider the [benefits of Code Capsules](https://www.codecapsules.io/compare).

Even if you don't intend to move to Code Capsules, this guide should help you create a general migration plan for most similar providers.

- [How to Emigrate from Heroku to Code Capsules](#how-to-emigrate-from-heroku-to-code-capsules)
  - [Document Your Heroku System](#document-your-heroku-system)
  - [An Example Full-Stack Application](#an-example-full-stack-application)
  - [Reviewing the App on Heroku](#reviewing-the-app-on-heroku)
    - [The Frontend](#the-frontend)
    - [The Backend](#the-backend)
    - [The Cron job](#the-cron-job)
    - [Summary of the System](#summary-of-the-system)
  - [Creating the App on Code Capsules](#creating-the-app-on-code-capsules)
    - [The Database](#the-database)
    - [The Backend and Frontend](#the-backend-and-frontend)
    - [The Cron job](#the-cron-job-1)
  - [Other Components and Cross-Cutting Concerns in Code Capsules](#other-components-and-cross-cutting-concerns-in-code-capsules)
  - [Next Steps](#next-steps)

## Document Your Heroku System

No matter where you plan to move your app, you first need to document all of its components and settings.

- Using either the Heroku CLI or web interface, list all apps to emigrate.
- Check each app's **Resources** tab (or `Procfile` in your repository) to see if the dyno is `web`, which has a public URL, or `worker`, which is for backend tasks only. Note if the dyno is a standard framework (like Python) or a custom Docker image. Also note how much CPU, RAM, and disk space it uses.
- List all addons for each app, like databases and schedulers, and their connection settings.
- Note the exact URLs, domains, and subdomains of each app and addon.
- Copy the settings (config vars) into a spreadsheet.
- Examine the deployment process of each app. If you are migrating to Code Capsules, deployment is done only with a corporate Git host. Organize your repositories so that each app and version (QA, production) are in their own branch, or subfolder in a branch. This allows each to be deployed separately.

If your database is large, it might be the most complicated component to emigrate. The simplest solution is to take your entire system offline, so that no new data is added. Then, backup your database from Heroku and restore it to your new host. However, if you don't want to take your app offline, you need to do a dual-write, which works roughly as follows:
- Create a database on your new host. Deploy your applications to the new host as well.
- Backup and restore static tables (enumerations) to the new database. These tables won't change, so can be done in advance.
- Point your new application to the database on the old host, but write new data to both databases — new and old. This allows you to do a gradual migration to the new host. Eventually you will be able to point all applications to the database on the new host and take the old database offline.

There are a lot of edge cases and reconciliation problems with dual-write. Consult a dedicated guide for more detail.

## An Example Full-Stack Application

Let's look at the most common type of application hosted on Heroku: a full-stack web app.  It needs:
- a static web server to serve the website (or a dynamic host in something like PHP or Node.js),
- a backend webservice the website talks to,
- a database for the web service to store user data,
- and some other backend service, like a cron job (scheduled task) that processes data in the database, analytics service, or email server.

In Heroku, each of these components is run in a separate "dyno" (container). This system is shown in the diagram below.

![System design](.gitbook/assets/app.svg)

Other common components include: object storage (to store images), QA versions of the live components, a message queue (like RabbitMQ), and an in-memory cache (like Redis). We discuss these at the end of the guide.

## Reviewing the App on Heroku

Let's work though moving our example app from Heroku to Code Capsules. We'll first discuss the components on Heroku, so you'll understand the peculiarities when planning your migration to another provider.

### The Frontend

On Heroku, you can create an app without assigning any dyno resources or deploying code yet. It acts as a placeholder. The dyno is created and run after you deploy your code, via a [`Procfile` file](https://devcenter.heroku.com/articles/procfile). This file might have an instruction like `web: node server.ts`, which tells Heroku to run a new web dyno.

All our code was in TypeScript and Node.js. But Heroku supports equivalent features in other languages to the ones we discuss in this guide.

Heroku doesn't support static websites. This was previously possible using a custom "buildpack", `heroku-buildpack-static`, but that's now deprecated. Instead, our example frontend dyno uses a Node.js static server — `http-server`.

Heroku has multiple ways to deploy code to a dyno. Review which you use:
- [API (HTTP calls)](https://devcenter.heroku.com/articles/build-and-release-using-the-api) — the simplest solution. Just zip your code and use `curl` to send the file to Heroku.
- CLI — an app you download and run in your terminal to connect to Heroku. This actually provides three ways to deploy to Heroku:
  1. Zip and push a build (same as the API)
  2. Use the CLI to add Heroku as a Git remote server to your app's local Git repository. Thereafter, you can `git push` to that remote and Heroku will deploy it.
  3. Build your own Docker image and push it to the Heroku Registry. You lose the PaaS benefits of automated security patching and runtime optimization.
- GitHub — Heroku will pull from your repository when you push to GitHub. (GitLab, BitBucket, and Codeberg are not supported.)

For a frontend to work correctly you need to set a backend URL, so the website knows where to talk to the web service. In Heroku, there are two ways to use settings:
1. You can set the values you need as ["config vars"](https://devcenter.heroku.com/articles/config-vars) in your dyno's **Settings** tab. The values will appear as environment variables at runtime. For example, in Node.js you could then access `process.env.DATABASE_URL`.
2. You can add the settings you need to a `.env` file along with your deployed code, accessing them in the same as you would a Heroku config var.

If you need to change a variable in any way before using it, you can add a postbuild step in your `package.json` file. Below is our example package file, which copied our backend URL to a JavaScript file that was included directly in our static HTML page:

```js
"scripts": {
    "heroku-postbuild": ". ./.env && echo \"const backendUrl = '${BACKEND_URL}';\" > config.js",
    "start": "http-server . -p $PORT"
  },
```

### The Backend

We needed to add our frontend app URL as a setting for the backend, so that we would not get CORS security errors when the two components talk to each other. Note that you need to deploy **both** frontend and backend to get both their URLs and set both environment variables before a system will work. You cannot deploy one component completely before the other.

The main difference with the backend is that it has access to the database. In Heroku, a database is not managed as an independent application. Instead, a database is an "addon" to the backend app, but other apps can be given access if necessary. You can create a PostgreSQL database in Heroku by adding it as an addon in the **Resources** tab of an app.

The database connection URL is automatically saved as a config var in the associated app, so your code can use the connection details as an environment variable. Since our application had only a single `user` table with only two columns, we created it in the backend code, without needing to run SQL scripts.

If you need to run database scripts or query data, Heroku exposes your database publicly. You can use a command like the one below to connect to the database. It shows our backend successfully saved the user's email address and plaintext password when registering on the site:

```sh
psql "$(curl -s https://api.heroku.com/apps/emigrate-backend/config-vars -H "Authorization: Bearer $KEY" -H "Accept: application/vnd.heroku+json; version=3"| jq -r '.DATABASE_URL')" -c 'SELECT * FROM "user"'

username | password
----------+----------
 t@t.com  | t

(1 row)
```

### The Cron job

Deploying a cron job is almost identical to deploying the backend in Heroku, with three differences:
- You need to specify the dyno type as a `worker` not a `web`.
- You need to attach the app to the existing database addon, instead of creating a new one.
- You need to add a scheduler addon to the app.

The scheduler addon is like a cron manager — it specifies when your app should run. Other than these times, the app isn't running, saving money. For our scheduler, we set it to run the command `node job.ts` every hour. The script this command runs connects to the database and prints every user — to mimic sending a welcome email or extracting analytics information.

You can see what that looks like below:

![App emailer](.gitbook/assets/appEmailer.webp)

### Summary of the System

Our Heroku system consists of three apps, shown below, with one database addon, and one scheduler addon.

![App components](.gitbook/assets/appComponents.webp)

## Creating the App on Code Capsules

In this section you'll learn how to deploy the app (and data) we created in Heroku on Code Capsules.

First we create a Code Capsules [Space](https://docs.codecapsules.io/platform/platform). Rather than the Heroku way of grouping applications by a globally unique name, Code Capsules allows you to group all components of an application inside a Space. We'll call our space `emigrate`.

![Code Capsules Space](.gitbook/assets/ccSpace.webp)

Next, you'll want to create a new capsule for each component you have in Heroku. The **Quickstart** tab in Code Capsules does not list all available Capsules. For example, it shows only three frontend frameworks, but you probably know that the JavaScript running in a user's browser is completely independent of the container type in the cloud that served it. So actually a frontend Capsule can serve any type of JavaScript framework.

![Code Capsules Quickstart](.gitbook/assets/ccQuickstart.webp)

You can see in the [documentation](https://docs.codecapsules.io/frontend) below that other Capsule types are available.

![Code Capsules available capsules](.gitbook/assets/ccAvailableCapsules.webp)

However, if you navigate to the Code Capsules **Capsules** tab and try to create a Capsule manually, it's more confusing. You can see in the screenshot that only three Capsule types are available — so how do we make a database Capsule?

![Code Capsules new capsule](.gitbook/assets/ccNewCapsule.webp)

There is actually a hidden scrollbar on this page. If you move your mouse over the box and click on the thin slider on the right, you'll be able to scroll down to other Capsule types.

![Code Capsules new capsule scrolled](.gitbook/assets/ccNewCapsuleDown.webp)

Now that we've seen all the Capsules available, let's choose which to use. Below is a mapping of the Heroku components to Code Capsules Capsules:

Component | Heroku | Code Capsules
---|---|---
emigrate-db | An addon attached to emigrate-backend | PostgreSQL Capsule
emigrate-backend  | Node.js Dyno | Backend Node.js Capsule
emigrate-frontend | Node.js Dyno | Frontend Node.js or static site Capsule
emigrate-emailer | Node.js worker Dyno, using Heroku Scheduler addon | Backend Node.js Capsule with internal scheduling

### The Database

We'll first deploy the component with no dependencies — the database. Unlike in Heroku, databases are their own entities in Code Capsules. We created a PostgreSQL database Capsule. You can see below the User, Space, and Capsule name.

![Code Capsules database setup](.gitbook/assets/ccDbSetup.webp)

Code Capsules does not provide any automated way to import or restore data. Instead, it provides connection details for you to connect to the database directly from your computer to query data.

So we need to export a database backup from the Heroku database and import it in the terminal to Code Capsules.

The easiest way to export your database from Heroku is to [use the CLI to create a database backup](https://devcenter.heroku.com/articles/heroku-postgres-backups), which is saved to AWS S3 as a file you can download.

If your database is only a few MB big however, you can connect to it directly and back it up to your local machine. We did this using the command below to create a backup file called `backup.dump`.

```sh
pg_dump -Fc --no-acl --no-owner "$(curl -s https://api.heroku.com/apps/emigrate-backend/config-vars -H "Authorization: Bearer $KEY" -H "Accept: application/vnd.heroku+json; version=3" | jq -r '.DATABASE_URL')" -f backup.dump
```

Connecting to databases in Code Capsules is more complicated than in Heroku, as Code Capsules does not provide a public URL for the database. Instead, you have to use [Code Capsules's CLI](https://docs.codecapsules.io/cli/readme/getting-started/quick-start). While you don't have to install the CLI permanently on your machine, you do need Node.js installed on your machine (or be able to run Node.js through Docker).

We used the command below to Node.js in a Docker container (to separate your app folder and the rest of your machine from malicious npm extensions), log in to Code Capsules, create a proxy connection to the PostgreSQL database, and restore the database.

To restore your database backup in Code Capsules, you need to change some settings in the command below. You need to use your own email address to login, and your own connection string details from your database Capsule (starting from the `npx` command).

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

Do not push <kbd>Q</kbd> to copy the connection string from the container as the CLI suggests. The CLI will crash. Instead, use the URL from the Capsule website details locally, as we did in the last two commands above.

If the commands above error, it may be due to intermittent network problems. If you're certain your settings are correct, simply retry until the database proxy initializes correctly.

![Code Capsules CLI](.gitbook/assets/ccCli.webp)

Once your database has restored, you can close the Docker container; you won't need it again unless you want to query the database.

### The Backend and Frontend

In Heroku, we used a Node.js Dyno for the frontend instead of a static site. In Code Capsules, static site frontend Capsules are available, so we chose that to host our website. A static site Capsule is simpler for your static web assets, but you'll still need a dynamic frontend Capsule is you're running something like Next.js, which has server-side rendering.

Frontend and backend Capsules look similar. There are only two differences:
- **Pricing**: A frontend Capsule's minimum configurable CPU resources (and therefore cost) are lower than a backend Capsule.
- **Internal networking**: A backend Capsule can access a database Capsule directly, while a frontend Capsule can't, and must talk to the database through a backend web server instead.

While choosing a Capsule size, you can set a custom size cheaper than the smallest standard size. Expand the custom radio-button at the bottom of the form to see more options.

![Code Capsules Capsule size](.gitbook/assets/ccCapsuleCheap.webp)

While Heroku has five ways to deploy code, Code Capsules has only one way — by pushing to a publicly available Git account. While the Code Capsules tutorials say that you need a GitHub account to deploy, they are incorrect. Code Capsules actually supports GitHub, GitLab, and BitBucket.

All three of these Git servers are domiciled in the US, and are subject to the US CLOUD Act (allowing the government to request customers' data). Code Capsules does not support an EU-centric solution like Codeberg for deployment. Code Capsules has to link its addon to your Git account, so you can't use a self-hosted GitLab installation either.

Although ultimately there is no guarantee your proprietary code will remain private on a corporate Git server, you can still keep your customer data safe. If you don't trust AWS as a US company, Code Capsules allows you to host Capsules on a European infrastructure provider, or even on your own server on your premises. To configure this for your account, please contact a support agent — it's not possible to choose infrastructure other than AWS by yourself.

For this guide, we chose GitHub to deploy to Code Capsules.

After choosing a frontend static site Capsule type, we linked the addon to our GitHub Organization.

![Code Capsules Install GitHub addon](.gitbook/assets/ccInstallGithub.webp)

In GitHub, choose which repositories Code Capsules should have access to. We deployed all three components in one repository, but your system probably has multiple repositories you need to enable.

![Code Capsules Install GitHub addon](.gitbook/assets/ccInstallGithubNew.webp)

If someone else in your organization has already linked Code Capsules to your Git provider, Code Capsules will error.

![Code Capsules GitHub error](.gitbook/assets/ccGithubError.webp)

In this case, the original user needs to authorize the repository and then share it in Code Capsules itself. This process is not documented, so you might need to speak to Code Capsules support in Slack for guidance.

Unlike Heroku, you cannot create a Capsule without linking it to a repository, so ensure your code is ready to deploy before setting up your infrastructure.

After choosing a repository, you can specify which folder Code Capsules should deploy. Since we had three components in different folders, we chose the `/frontend` folder. If you have one application per repository, you can just leave Code Capsules to use the `/` root folder.

![Code Capsules Capsule path](.gitbook/assets/ccCapsulePath.webp)

Finally, you enter a command to run when starting your Capsule. Like Heroku, you don't have to include dependency installation, like `npm install`. Code Capsules does that automatically before running your start command. For our static site, no run command is needed.

![Code Capsules Capsule command](.gitbook/assets/ccCapsuleCommand.webp)

Nowhere in the process above did we choose which framework to run our code with. Unlike Heroku with its Procfiles, Code Capsules detects your framework automatically.

Deploying the backend code was identical to the frontend, except for using a backend Capsule type, and specifying a run command.

If you browse to the logs you can check if your server is running. Our example backend did not run, since Code Capsules's Node.js default version is old (maintenance version 20), and so does not support TypeScript files (which requires long-term-support version 24).

```sh
2026-02-18T15:19:50.617
Node.js v20.20.0

2026-02-18T15:18:38.324
TypeError [ERR_UNKNOWN_FILE_EXTENSION]: Unknown file extension ".ts" for /workspace/git-source/backend/server.ts
```

We needed to update our required Node.js engine in at the bottom of our `package.json` file. Be sure to specify the equivalent for your framework, like Python or PHP.

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

After redeploying, the Capsule restarted using the correct Node.js version and our backend worked.

Next — environment variables. Code Capsules is similar to Heroku here. Heroku has config vars and Code Capsules has a **Config** tab with a list of environment variables.

![Code Capsules Capsule config](.gitbook/assets/ccCapsuleConfig.webp)

To add a connection from the backend to the database, click your database Capsule at the bottom left. In the details that appear, click the plus icon to a value as an environment variable to the current Capsule. You can edit the variable name to match whatever you have in your code already, like `DATABASE_URL`.

![Code Capsules Capsule config values from another capsule](.gitbook/assets/ccCapsuleConfig2.webp)

Save your variables at the top right and restart the Capsule.

Like in Heroku, both frontend and backend apps must be deployed before you can get the URLs of them both. You then set both URLs as environment variables, so they can call each other. While Code Capsules provides the URLs, it does not prefix them with `https://`, which we needed to do before our apps ran without error.

Static sites cannot access environment variables at runtime, so you must include in your build process a way to inject the backend URL into the JavaScript of the website.

The final step was to delete the Heroku `Procfile`s from our repository, as they are not needed in Code Capsules.

### The Cron job

Unlike Heroku, Code Capsules doesn't support scheduled tasks or cron jobs. Instead, you need to deploy your code as a backend Capsule that is always running and manages its own scheduling. For our Node.js example, we could use node-cron or JavaScript's native `setInterval()`. We chose `node-cron` and added it as a dependency.

Our makeshift scheduler code is shown below.

```js
async function emailJob() {
  const { rows } = await pool.query(`SELECT username FROM "user"`);
  for (const row of rows) console.log(`Emailing ${row.username}`);
}

cron.schedule("0 * * * *", emailJob);
```

![Code Capsules emailer](.gitbook/assets/ccEmailer.webp)

There are some dangers in managing scheduling internally:
- **Memory persistence**: If your Capsule restarts during a task, the schedule is lost until the process starts again.
- **Scaling**: If you scale your app to more than one Capsule, the schedule will run on every container simultaneously, duplicating the work. To prevent this, you need a distributed lock (like Redis).
- **Missed executions**: If the Capsule is down at the exact second a job is scheduled, the scheduler will not run missed jobs once it comes back online.

Another scheduling option is to use an external cron service (like [cron-job.org](https://cron-job.org)), and have it call an HTTP endpoint you add to your emailer (or backend).

## Other Components and Cross-Cutting Concerns in Code Capsules

We've discussed how to move common system components to Code Capsules, but you might have a few more:

- **Object storage (file storage)**: Object storage, like AWS S3, is used for storing binary files like photos, for which databases are not suitable. Code Capsules has a file storage Capsule type. Like the database Capsule, the storage Capsule exposes an environment variable for other backend Capsules to use to access it. The variable is just a file path, like `/mnt/storage`, which other Capsules can write to like a normal file system.
- **Domains**: Each Capsule exposes a URL in the **Domains** tab you can use to create a CNAME record with your DNS provider, so you can use your company's domain name.
- **QA environments**: While Heroku provides [pipelines](https://devcenter.heroku.com/articles/pipelines) to automatically provision testing and production versions of dynos, Code Capsules has no such thing. To create a test environment for a Capsule, you need to manually create a new Capsule that points to a specific Git branch in your repository, like `/qa`.
- **Blue/green deployments**: A blue/green deployment is a technique to achieve zero service downtime. Instead of deploying a new code version to your live server, you deploy to another server and then instantly rotate the live URL to the new deployment. This is not possible in Code Capsules. Instead, there will be a delay and your app will be offline while your code is deployed to your live Capsule and the Capsule restarts, downloads dependencies, and runs your start command.
- **CLI automation**: Heroku provides an API and CLI that allow you to write scripts to automate creating, editing, scaling, and deleting dynos. Code Capsules does not. All Capsule creation and management must be done by hand in the web interface. The only alternative is to use an AI agent, like Amp or Claude, to interact with the website on your behalf.
- **Monitoring, logs, and dashboards**: Code Capsules has tabs for this for each Capsule. There is no higher-level system dashboard for the whole Space though.
- **Backups**: You can configure a Code Capsules database to automatically backup your database regularly, and you can create manual backups at any time. Backups can be restored with one click.
- **Scaling**: You can manually increase the RAM, CPU, or disk space of a Capsule, as well as increase the number of instances (replicas) from one to three. Code Capsules does not provide the capability to automatically scale your Capsules under load.
- **Message queues and caches**: Components like RabbitMQ and Redis can be deployed in their own backend Capsule, which other Capsules can talk to though HTTP. There is a dedicated Redis Capsule type.
- **Custom Docker containers**: If your Heroku app uses a framework that Code Capsules doesn't support, like .NET or Ruby, you can deploy a custom Docker image. To do this, create a Capsule using the Docker type. Point the Capsule to a repository that has your code and a Dockerfile that specifies how to build a Docker image to run your code. Below is an example Dockerfile from the Code Capsules Python Flask tutorial:
  ```dockerfile
  # syntax=docker/dockerfile:1

  FROM python:3.8-slim-buster

  WORKDIR /python-docker

  COPY requirements.txt requirements.txt
  RUN pip3 install -r requirements.txt

  COPY . .

  CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0"]
  ```
  Code Capsules supports only Dockerfiles, and not Docker compose files and prebuilt Docker images. However, you can prebuild a Docker image on your local computer, upload it to DockerHub, and point your Dockerfile to that image, with no other build steps required.
- **Running multiple applications in a Capsule**: If you want to run two different services in one Capsule, like a PHP and Ruby server, you need to use a custom Dockerfile that includes both frameworks.

## Next Steps

Interested in trying Code Capsules? [Create a free account](https://app.codecapsules.io/) to take a look around, or ask us anything on [Slack](https://codecapsules.slack.com/join/shared_invite/zt-krsv5ott-_WR~S44xGmjATdpMsRC7yg#/shared-invite/email).