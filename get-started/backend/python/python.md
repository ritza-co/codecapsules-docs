# Python

In this guide, we'll clone an existing Python repository and take a look at how to edit the project locally before pushing the changes to GitHub. The final step will be to deploy the application with the changes we made locally to Code Capsules.

## Clone Starter Project

We'll be using an example starter project provided by Code Capsules on their [GitHub repository](https://github.com/codecapsules-io/demo-python). Fork this repository by clicking **Fork** at the top-right of your screen and selecting your GitHub account as the destination. Clone the forked repository to your machine by clicking the green **Code** button and copying the link that appears in the pop-up.

Open a terminal window on your machine and navigate to your preferred working directory. Run `git clone <FORKED-REPO-URL>`, replacing `<FORKED-REPO-URL>` with the link you copied from the pop-up above.

## Run Starter Project Locally

Before we can run the project locally, we need to create and activate a virtual environment where we'll install the project's dependencies. Depending on your machine, run the appropriate commands from the ones listed below:

MacOS

```
python3 -m venv env
source env/bin/activate
```

Windows

```
py -m venv env
.\env\Scripts\activate
```

Next, install the project's dependencies by running `pip install -r requirements.txt` in the same terminal window where your environment is activated.

To start the application, run the command `python3 -m flask run` in the same terminal window, and you should see the application in your browser.

## Make Changes

Open the `app/routes.py` file in your text editor and add the code snippet below to add another route to the app:

```py
@app.route('/greeting')
def greeting():
    return "Hello there, thank you for visiting my new route"
```

You need to stop and restart the server in order to see your changes.

## Push the Changes

Add and commit the changes you made by running the commands below in a terminal window while in the project's root folder:

```
git add app/routes.py
git commit -m "Added new route"
```

Push the changes to your remote repository by running `git push` in the same terminal window.

## Create an Account with Code Capsules

To create a new Space, log in to your Code Capsules account, click the yellow `+` icon on the bottom left of the screen, and select **New Space**.

Follow the prompts, choosing your region and giving your Space a name, then click **Create Space**.

![Create a Space](../../.gitbook/assets/space-name.png)

## Create the Capsule

A [Capsule](https://app.gitbook.com/s/gIlxo9gU7Lotj1cdGRh6/capsules/what-is-a-capsule) provides the server for hosting an application on Code Capsules.

To create a new Capsule for your Space, follow the instructions below:

1. Choose **Backend Capsule**, your Team, and Space.
2. Choose your payment plan.
3. Click the GitHub button and give access to the repository you forked at the start of the tutorial.
4. Choose the GitHub repository you forked.
5. Press **Next**.
6. Leave **Run Command** blank.
7. Click **Create Capsule**.

Code Capsules will automatically build your application when you’ve finished creating the Capsule.

Once your application is live, you can view the build log by selecting the **Deploy** tab and clicking the **View build log** link in the **Builds** section.

![Build Logs](/broken/files/m10b9QRjcH8SGo8JjPp9)

Once the build is complete, a URL link will appear in the URL section in the **Details** tab. Click the link, and you should see your deployed application.

![Deployed App](../../.gitbook/assets/cc-flask-app.png)

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](../../).
