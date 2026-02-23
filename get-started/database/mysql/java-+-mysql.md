---
description: A guide on how to launch a Java MySQL application from GitHub.
---

# Java + MySQL

Deploy a Java MySQL application and learn how to host backend code on Code Capsules.

## Setup

Code Capsules connects to GitHub repositories to deploy applications. To follow this guide, you'll need a [Code Capsules](https://codecapsules.io/) account and a [GitHub](https://github.com/) account.

To demonstrate how to deploy a Java MySQL application with Code Capsules, we've provided an example application, which you can find on the [Code Capsules GitHub repository](https://github.com/codecapsules-io/demo-java-mysql).

Sign in to GitHub, and fork the example application by clicking **Fork** at the top-right of your screen and selecting your GitHub account as the destination.

## Create the Capsules

A [Capsule](https://app.gitbook.com/s/gIlxo9gU7Lotj1cdGRh6/capsules/what-is-a-capsule) provides the server for hosting an application on Code Capsules.

Navigate to the Spaces tab and open the Space you'll be using.

Click the yellow **+** button in the bottom left of the screen, select **New Capsule**, and follow the instructions below to create a Data Capsule:

1. Choose **Data Capsule**.
2. Under **Data Type**, select **MySQL Database Cluster**.
3. Click **Create Capsule**.

Navigate to the **Space** containing your recently created Data Capsule and click the **New Capsule** button. Follow the instructions below to create a Backend Capsule:

1. Choose **Backend Capsule**, your Team, and Space.
2. Choose a payment plan.
3. Choose the GitHub repository you forked.
4. Press **Next**.
5. Leave **Run Command** blank.
6. Click **Create Capsule**.

Code Capsules will automatically build your application when you've finished creating the Capsule. While the build is in progress, you can view the log by clicking **View Build Progress** next to the **Building Capsule** message.

Once your application is live, you can view the build log by selecting the **Deploy** tab and clicking the **View build log** link in the **Builds** section.

![Build Logs](<../../.gitbook/assets/backend-capsule-build-logs (1).png>)

## Connect the Capsules

After the two capsules have been successfully built, the next step is to connect them. To do this, navigate to the **Config** tab of your Backend Capsule. Scroll down to the **Data Capsule** section and click **View** to view the environment variables from the Data Capsule. Click the `+` next to the `Connection string` variable to create a `DATABASE_URL` environmental variable in your Backend Capsule, which gives access to services and features of your Data Capsule.

![Connect Data Capsule](../../.gitbook/assets/sql-bind-env.png)

## View Application

You can now view the application after the two capsules have been connected. To see how it looks, click on the URL in the **Details** tab.

![Deployed App](../../.gitbook/assets/java-mysql-app.png)

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](../../).
