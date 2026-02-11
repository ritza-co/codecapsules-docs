# Static File Share with Flask and Caddy

This guide will show you how to deploy a basic file-sharing site on Code Capsules. You'll use a Flask application to upload files and a Caddy file server to serve and download the uploaded files.

We'll use three capsules: a Data Capsule to store the files, a Backend Capsule for the Flask upload server, and a Docker Capsule for the Caddy file server.

After following the guide, you'll be able to upload files in the Flask application, see a file listing, and get public links to your uploaded files in the Caddy application, as shown below.

![Flask upload server and Caddy file server](../../.gitbook/assets/upload-download.png)

## Setup

Code Capsules connects to GitHub repositories to deploy applications. To follow this guide, you'll need a [Code Capsules](https://codecapsules.io/) account and a [GitHub](https://github.com/) account.

The code you need for this guide is in the following GitHub projects.

* [**Flask file uploads**](https://github.com/codecapsules-io/flask-file-uploads)
* [**Caddy file server**](https://github.com/codecapsules-io/caddy-file-server)

Sign in to GitHub, and fork both example applications by clicking **Fork** at the top right of your screen and selecting your GitHub account as the destination.

## Create a Space for Your App

Log in to your Code Capsules account and navigate to the **Spaces** tab. Once there, click the yellow `+` icon on the top right of the screen to add a new Space.

Follow the prompts, choosing your region and giving your Space a name, then click **Create Space**.

![Create a Space](/broken/files/c1RKSsytKv66iCGB7Ln3)

Example instructions to go with numbered annotations

1. Choose a Team — you can use a default **personal** Team if you're the only person working on this project, or a named Team if you're collaborating with others
2. This should remind you of the project, for example, **customer-api** or **notetaking-app**
3. Choose a country close to where most of your users will be

## Create the Capsule

A [Capsule](https://app.gitbook.com/s/gIlxo9gU7Lotj1cdGRh6/capsules/what-is-a-capsule) provides the server for hosting an application on Code Capsules.

Navigate to the Capsules tab. Once there, click the yellow `+` icon on the bottom left of the screen to add a new Capsule.

### Data Capsule

To create a new Data Capsule for your Space, follow the instructions below:

1. Choose **Persistent Storage**, your Team, and Space.
2. Choose your payment plan.
3. Click **Create Capsule**.

### Backend Capsule

Navigate to the Space containing your recently created Data Capsule and click the yellow `+` icon on the bottom left of the screen. Follow the instructions below to create a Backend Capsule:

1. Choose **Backend Capsule**, your Team, and Space.
2. Choose your payment plan.
3. Select the `flask-file-uploads` repository you forked at the start of the tutorial.
4. Choose the GitHub repository you forked.
5. Press **Next**.
6. Leave **Run Command** blank.
7. Click **Create Capsule**.

### Docker Capsule

Click the **New capsule** button again.

1. Choose Docker Capsule
2. Select the caddy-file-server repository
3. Click **Next**
4. Enter `Dockerfile` for the Dockefile location
5. Press **Create Capsule**

Code Capsules will automatically build your application when you've finished creating the Capsule. While the build is in progress, you can view the log by clicking **View Build Progress** next to the **Building Capsule** message.

Once your application is live, you can view the build log by selecting the **Deploy** tab and clicking the **View build log** link in the **Builds** section.

![Build Log](/broken/files/m10b9QRjcH8SGo8JjPp9)

## Binding the Capsules

Now navigate to each of the Backend Capsule and Docker Capsule and bind them to the Data Capsule. This gives them access to a shared file store so that the Flask application can upload files and the Caddy server can serve them to users.

## View Application

Once the build is complete, click the **URL** link in the **Config** tab, and you should see your deployed application.

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](../../).
