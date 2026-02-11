---
description: A guide on how to deploy an HTML5 template from GitHub.
---

# HTML5UP Template

Deploy an HTML5 template and learn how to host frontend code on Code Capsules.

## Setup

Code Capsules connects to GitHub repositories to deploy applications. To follow this guide, you'll need a [Code Capsules](https://codecapsules.io/) account and a [GitHub](https://github.com/) account.

To demonstrate how to deploy an HTML5 template to Code Capsules, we'll be using a template from [HTML5 UP](https://html5up.net/). Head over to the HTML5 UP site and download the zip file for any template you find there. Unzip this template file in your preferred working directory on your local machine.

## Create a Repository

Sign in to GitHub and create a repository for the template site you downloaded.

We'll need to push the unzipped template files to your newly created repository for Code Capsules to deploy the template site from your GitHub account. To do this, initialize a git repository in the project's root folder on your machine by running the command `git init` from a terminal window while in the root folder.

## Push to GitHub

Before you can push to GitHub, you need to add the untracked files to your local repository. Run `git add -A` in a terminal window from the project's root folder to do so. After adding the files, commit your changes by running `git commit -m "Initial app commit"`.

Run the command below to set the remote repository for your local repo. Be sure to replace `<YOUR-REMOTE-GITHUB-URL>` with the actual URL for your remote repository.

```
git remote add origin <YOUR-REMOTE-GITHUB-URL>
```

Push the unzipped files to your remote repository by running `git push origin main`.

## Create an Account with Code Capsules

Log in to your Code Capsules account and navigate to the **Spaces** tab. Once there, click the yellow `+` icon on the top right of the screen to add a new Space.

Follow the prompts, choosing your region and giving your Space a name, then click **Create Space**.

![Create a Space](/broken/files/c1RKSsytKv66iCGB7Ln3)

Example instructions to go with numbered annotations

1. Choose a Team — you can use a default **personal** Team if you're the only person working on this project, or a named Team if you're collaborating with others
2. This should remind you of the project, for example, **customer-api** or **notetaking-app**
3. Choose a country close to where most of your users will be

## Create the Capsule

A [Capsule](https://app.gitbook.com/s/gIlxo9gU7Lotj1cdGRh6/capsules/what-is-a-capsule) provides the server for hosting an application on Code Capsules.

To create a new Capsule for your Space, follow the instructions below:

1. Choose **Frontend Capsule**, your Team, and Space.
2. Choose your payment plan.
3. Click the GitHub button and give access to the repository you forked at the start of the tutorial.
4. Choose the GitHub repository you forked.
5. Press **Next**.
6. Leave **Build Command** and the **Static content folder path** blank.
7. Click **Create Capsule**.

Code Capsules will automatically build your application when you've finished creating the Capsule.

Once your application is live, you can view the build log by selecting the **Deploy** tab and clicking the **View build log** link in the **Builds** section.

![Build Logs](../../.gitbook/assets/frontend-build-logs.png)

Once the build is complete, a URL link will appear in the URL section in the **Details** tab. Click the link, and you should see your deployed application.

![Deployed App](../../.gitbook/assets/html5up-site.png)

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](../../).
