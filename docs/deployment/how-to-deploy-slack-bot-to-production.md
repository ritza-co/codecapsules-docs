---
title: Deploy a Node.js Slack Bot
description: A guide to deploying a Slack Bot from GitHub.
hide:
  - navigation
---

# How to Deploy a Node.js Slack Bot to Production on Code Capsules

Deploy a Node.js Slack Bot and learn how to host backend code on Code Capsules.

## Set up

Code Capsules connects to GitHub repositories to deploy applications. To follow this guide, you’ll need a [Code Capsules](https://codecapsules.io/) account and a [GitHub](https://github.com/) account.

To demonstrate how to deploy a Node.js Slack Bot with Code Capsules, we’ve provided an example bot which you can find on the [Code Capsules GitHub repository](https://github.com/codecapsules-io/slackbot).

Sign in to GitHub, and fork the example bot repository by clicking "Fork" at the top-right of your screen and selecting your GitHub account as the destination.

## Create a Space for your Bot

Log in to your Code Capsules account and navigate to the "Spaces" tab. Once there, click the yellow `+` icon on the top right of the screen to add a new Space. 

Follow the prompts, choosing your region and giving your Space a name, then click "Create Space".

![space name](../assets/deployment/shared/space-name.png)

1. Choose a team — you can use a default “personal” team if you’re the only person working on this project, or a named team if you’re collaborating with others.
2. This should remind you of the project, for example “customer-api” or “notetaking-app”.
3. Choose a country close to where most of your users will be.
4. If you’re already using a specific cloud, you can choose that here, otherwise pick any one.

## Create the Capsule

A [Capsule](https://codecapsules.io/docs/FAQ/what-is-a-capsule/) provides the server for hosting an application on Code Capsules.

To create a new Capsule for your space follow the instructions below:

1. Choose "Backend Capsule", your Team and Space.
2. Choose your payment plan.
3. Click the GitHub button and give access to the repository you forked at the start of the tutorial.
4. Choose the GitHub repository you forked.
5. Press "Next".
6. Leave "Run Command" blank.
7. Click "Create Capsule".

Code Capsules will automatically build your application when you’ve finished creating the Capsule. 

Once your application is live, you can view the build log by selecting the "Deploy" tab and clicking the "View build log" link in the "Builds" section.

![Build logs](../assets/deployment/shared/backend-capsule-build-logs.png)

## Register the Bot

You'll need a Slack user account and to log into a workspace before you can create a Slack bot. After you have logged into a workspace head over to the [Slack API](https://api.slack.com/) page and click on "Create an app".

1. On the popup that displays, choose the "From scratch" option, give your app a name and choose the workspace you want to add it to then click "Create App".
2. Click "OAuth & Permissions" on the left panel and scroll down to the *Scopes* section and click "Add an OAuth Scope" under the *Bot Token Scopes* section.
![select scopes slack](../assets/tutorials/build-slackbot-with-node/slack-scopes.png)
3. Choose "chat:write" from the options list.
4. Click on the "Slash Commands" menu item on the left, under Features then click "Create a new Command".
![slash command menu](../assets/tutorials/build-slackbot-with-node/choose-slash-command.png)
5. Give the command the name `/stats`.
6. For the Request URL, head over to your Capsule "Details" tab on Code Capsules and copy the URL under the "URL" section.
![code capsules domain](../assets/deployment/shared/capsule-domain.png)
7. Paste your URL into the Request URL box on Slack, and add `/slack/command/stats` to the end of it.
8. Fill in the description with 'Returns key stats from the app'.
![create new command settings](../assets/tutorials/build-slackbot-with-node/create-command.png)
9. Click "Save". 

## Add Environment Variables

Our bot's logic makes use of environment variables to function securely. You'll need to add the variables to your capsule's "Configure" tab before the bot can send/receive messages from your workspace. 

For the first environment variable, `SLACK_BOT_TOKEN`: 

1. Head over to your app dashboard on Slack, and click on the "OAuth & Permissions" menu item on the left-hand side. 
2. Click the "Install to Workspace" button followed by the "Allow" button. 
3. Actioning the preceeding 2 steps should reveal a newly generated "Bot User OAuth Token". Copy this token. 
4. On your capsule's "Configure" tab, add a new environment variable with the name `SLACK_BOT_TOKEN` and set its value to the token you copied in the previous step. 
![add token environment variable](../assets/tutorials/build-slackbot-with-node/token-env-variable.png)
5. Confirm your changes by clicking on "Save".

For the second environment variable, `SLACK_CHANNEL_ID`:

1. Open Slack and right-click on the channel name of the channel you want to send messages to.
2. On the context menu that appears, choose "Copy Link".
![copy channel link from Slack](../assets/tutorials/build-slackbot-with-node/copy-channel-link.png)
3. Paste the link in a browser tab but don't press "enter" or "return".
4. The link should follow the format, `https://<workspace-name>.slack.com/archives/C01SZ6Z3TCY`. The last part of that URL is the channel ID; in this example case, `C01SZ6Z3TCY`.
5. Add the channel ID to the environment variables with the name `SLACK_CHANNEL_ID`.
![add slack channel ID environment variable](../assets/tutorials/build-slackbot-with-node/channel-env-variable.png)
6. Confirm your changes by clicking on "Save".

For the third environment variable, `SLACK_SIGNING_SECRET`:

1. Head over to your Slack app dashboard and click on "Basic Information" on the left-hand side.
2. Scroll down to *App Credentials* and look for the *Signing Secret*. Click "Show" and copy the secret.
![copying signing secret from slack](../assets/tutorials/build-slackbot-with-node/slack-signing-secret.png)
3. Add a new environment variable with the name `SLACK_SIGNING_SECRET` on your capsule's "Configure" tab and set its value to the secret you copied in the previous step.
![setting signing env variable on Code Capsules](../assets/tutorials/build-slackbot-with-node/signing-env-variable.png)
4. Confirm your changes by clicking on "Save", then restart your Capsule by toggling the radio button in the top right off and on again.

## Invite Bot to Channel

You need to invite the bot to the chosen channel so that it'll be able to post there. To do this, send a message mentioning `@<bot-name>` the bot in the channel you added it to. Click "Invite Them" when Slack prompts you.

![invite bot to channel](../assets/tutorials/build-slackbot-with-node/invite-bot.png)

## Chat with the Bot 

The bot can now send messages to the channel everytime it starts. You can restart the capsule to see this functionality in action or alternatively send a `/stats` message in the channel to get a report about the bot's status on Code Capsules.

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](/docs/deployment/).