---
title: Deploy a Laravel Docker API to Production
description: Publish a Caddy Docker site using its source code on GitHub.
hide:
  - navigation
---

# How to Deploy a Laravel Docker API to Production on Code Capsules

Deploy a Laravel Docker API and learn how to host backend code on Code Capsules.

## Set up

Code Capsules connects to GitHub repositories to deploy applications. To follow this guide, you’ll need a [Code Capsules](https://codecapsules.io/) account and a [GitHub](https://github.com/) account.

To demonstrate how to deploy a Laravel Docker API with Code Capsules, we’ve provided an example application, which you can find on the [Code Capsules GitHub repository](https://github.com/codecapsules-io/laravel-docker-api).

Sign in to GitHub, and fork the example application by clicking "Fork" at the top right of your screen and selecting your GitHub account as the destination.

## Create an Account with Code Capsules

If you don’t already have an account, navigate to the [Code Capsules](https://codecapsules.io/) site and click the "Sign Up" button in the top right corner of the screen. Enter your details to create an account, or log in to an existing one.

If you’ve just signed up for an account, you’ll be directed to a welcome page on your first log in. Click on the "Go To Personal Team" button.

Alternatively, if you’re signing in again, click on "Spaces" in the top right corner of your screen.

Code Capsules gives every account a Personal Team by default. A Team is an environment for you to manage your Spaces and Capsules. For a better understanding of Teams, Spaces, and Capsules, take a look at [our explanation](https://codecapsules.io/docs/FAQ/teams-spaces-capsules/).

## Create a Space for your Apps

[Spaces](https://codecapsules.io/docs/FAQ/what-is-a-space/) are an organizational tool for your applications. You can select the Personal Space that you find in your default Personal Team to host this application, or you can create a new Space. In the Spaces Tab, click the "Create A New Space For Your Apps" button.

Follow the prompts, choosing your region and giving your Space a name, then click "Create Space".

![space name](../assets/deployment/html/space-name.png)

## Link to GitHub

To link to GitHub, click your profile image at the top right of the Code Capsules screen and find the "GitHub" button under "GitHub Details".

![git-button](../assets/deployment/html/git-button.png)

Click the "GitHub" button, select your GitHub username, and do the following in the dialog box that appears:

1. Select "Only Select Repositories".
2. Choose the GitHub repository we forked.
3. Press "Install & Authorize".

![Install & authorize github](../assets/deployment/html/github-integration.png){ width="75%" }

## Add Repository to Team

Select "Team Settings" in the top navigation bar to switch to the Team Settings tab.

Click on the "Modify" button under the Team Repos section, and an "Edit Team Repos" screen will slide in from the right. Click "Add" next to the demo repo, and then "Confirm". All the Spaces in your Team will now have access to this repo.

![Edit Team Repos](../assets/deployment/html/team-repos.gif)

## Create the Capsule

A [Capsule](https://codecapsules.io/docs/FAQ/what-is-a-capsule/) provides the server for hosting an application on Code Capsules.

Navigate to the "Spaces" tab and open the Space you’ll be using.

Click the "Create a New Capsule for Your Space" button, and follow the instructions below:

1. Choose "Docker Capsule".
2. Under "Product", select "Sandbox".
3. Choose the GitHub repository you forked.
4. Press "Next".
5. Enter "Dockerfile" as the input in the "Dockerfile location" field.
6. Leave the "Docker build context" field blank.
7. Click "Create Capsule".

![Create Docker capsule](../assets/deployment/caddy-docker/docker-guide.gif){ width="75%" }

Code Capsules will automatically build your application when you’ve finished creating the Capsule. While the build is in progress, you can view the log by clicking "View Build Progress" next to the "Building Capsule" message.

Once your application is live, you can view the build log by selecting the "Deploy" tab and clicking the "View build log" link in the "Builds" section.

![Build logs](../assets/deployment/html/frontend-capsule-build-logs.png){ width="80%" }

Once the build is complete, navigate to the "Configure" tab and scroll down to the "Network Port" section. Enter "8000" as the port number and click on "Update Capsule".

![Network Port](../assets/deployment/docker-laravel/network-port.png)

The Laravel docker API will now be live, and you can click on the "Live Website" link at the top right of the tab and navigate to the `/api` route to view it.

![Deployed App](../assets/deployment/docker-laravel/docker-laravel.png)

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](/docs/deployment/).
