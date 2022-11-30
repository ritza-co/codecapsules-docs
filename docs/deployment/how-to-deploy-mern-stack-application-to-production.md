---
title: Deploy a MERN Stack Application to Production
description: A guide to deploying a MERN Stack application from GitHub.
hide:
  - navigation
---

# How to Deploy a MERN Stack Application to Production on Code Capsules

*A video for this guide can be found [here](https://www.youtube.com/watch?v=iHl-pFlLWk0&list=PLoEGujFfB4nYx8b4Wai0l75KsjzEruy6l&index=1&t=1s) if you prefer watching to reading.*

Deploy a MERN stack application and learn how to host backend code on Code Capsules.

## Set up

Code Capsules connects to GitHub repositories to deploy applications. To follow this guide, you’ll need a [Code Capsules](https://codecapsules.io/) account and a [GitHub](https://github.com/) account.

To demonstrate how to deploy a MERN stack application with Code Capsules, we’ve provided an example application which you can find on the [Code Capsules GitHub repository](https://github.com/codecapsules-io/mern-stack).

Sign in to GitHub, and fork the example application by clicking "Fork" at the top-right of your screen and selecting your GitHub account as the destination.

## Create an Account with Code Capsules

If you don’t already have an account, navigate to the [Code Capsules](https://codecapsules.io/) site and click the "Sign Up" button in the top right corner of the screen. Enter your details to create an account, or log in to an existing one.

If you’ve just signed up for an account, you’ll be directed to a welcome page on your first login. Click on the "Go To Personal Team" button.

Alternatively, if you’re signing in again, click on the "Spaces" tab.

Code Capsules gives every account a Personal Team by default. A Team is an environment for you to manage your Spaces and Capsules. For a better understanding of Teams, Spaces, and Capsules, take a look at [our explanation](https://codecapsules.io/docs/FAQ/teams-spaces-capsules/).

## Create a Space for your App

Log in to your Code Capsules account and navigate to the "Spaces" tab. Once there, click the yellow `+` icon on the top right of the screen to add a new Space. 

Follow the prompts, choosing your region and giving your Space a name, then click "Create Space".

![space name](../assets/deployment/shared/space-name.png)

1. Choose a team — you can use a default “personal” team if you’re the only person working on this project, or a named team if you’re collaborating with others.
2. This should remind you of the project, for example “customer-api” or “notetaking-app”.
3. Choose a country close to where most of your users will be.
4. If you’re already using a specific cloud, you can choose that here, otherwise pick any one.

## Create the Capsule

A [Capsule](https://codecapsules.io/docs/FAQ/what-is-a-capsule/) provides the server for hosting an application on Code Capsules.

Navigate to the "Capsules" tab. Once there, click the yellow `+` icon on the top right of the screen to add a new Capsule.

To create a new Data Capsule for your Space follow the instructions below:

1. Choose "MongoDB", your Team and Space.
2. Choose your payment plan.
3. Click "Create Capsule".

Navigate to the "Space" containing your recently created Data Capsule and click the yellow `+` icon on the top right of the screen. Follow the instructions below to create a Backend Capsule:

1. Choose "Backend Capsule", your Team and Space.
2. Choose your payment plan.
3. Click the GitHub button and give access to the repository you forked at the start of the tutorial.
4. Choose the GitHub repository you forked.
5. Press "Next".
6. Leave "Run Command" blank.
7. Click "Create Capsule".


Code Capsules will automatically build your application when you’ve finished creating the Capsule. While the build is in progress, you can view the log by clicking "View Build Progress" next to the "Building Capsule" message.

Once your application is live, you can view the build log by selecting the "Deploy" tab and clicking the "View build log" link in the "Builds" section.

![Build logs](../assets/deployment/shared/backend-capsule-build-logs.png)

## Binding the Capsules

Now you need to bind the two capsules together. Navigate to the "Config" tab of the Backend Capsule you've just created. Scroll to the "Bind Data Capsule" section and click on the "Bind". This enables the capsule to use the MongoDB database in the Data Capsule. 

![Bind MEAN Capsules](../assets/deployment/shared/bind-mongodb.png)

## Edit `DATABASE_URL` Environment Variable

Once the binding is complete, you have to append `/app?authSource=admin` to the `DATABASE_URL` value under the "Environment Variables" section on the "Config" tab. 

![Edit DATABASE_URL Environment Variable](../assets/deployment/mern/edit-database-url.png)

Confirm your changes by clicking on "Save" then restart your capsule by toggling the radio button in the top right off and on again.

## View Application

After restarting the capsule, the application will now be ready to be viewed. Click the URL link in the "Details" tab of the capsule tab and you should see your deployed application.

![Deployed App](../assets/deployment/mern/cc-mern-stack.png)

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](/docs/deployment/).

