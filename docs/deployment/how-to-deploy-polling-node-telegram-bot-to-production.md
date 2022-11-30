---
title: Deploy a Node.js Telegram Bot
description: A guide to deploying a Node.js Telegram Bot from GitHub.
hide:
  - navigation
---

# How to Deploy a Node.js Telegram Bot to Production on Code Capsules For Free in 5 Minutes

*This guide uses polling, which is easier to deploy but not recommended. See [this guide](./how-to-deploy-node-telegram-bot-to-production.md) for how to implement the same bot using webhooks.*

Deploy a Node.js Telegram Bot and learn how to host backend code on Code Capsules for free.

## Register the Bot

You'll need a Telegram user account before you can create a Telegram bot. Head over to Telegram and create an account if you don't already have one.

When you've signed in to Telegram, search for "BotFather" (a bot for managing all other Telegram bots) and start a new chat with it. Follow the steps below to register a new bot with the BotFather:

1. Type `/start` and press send.
2. Type `/newbot` and press send.
3. Choose a name for your bot.
4. Choose a username for your bot that ends in "bot".

The BotFather will respond with a message containing an access token for your newly created bot. This access token will allow our application to access the Telegram API and tell our bot what to do when receiving different messages from users.

To confirm that your bot was created successfully, search for the bot's username. You should be able to see it and start a conversation with it, although it won't respond as we haven't written the bot's logic yet.

## Set up

Code Capsules connects to GitHub repositories to deploy applications. To follow this guide, you’ll need a [Code Capsules](https://codecapsules.io/) account and a [GitHub](https://github.com/) account.

To demonstrate how to deploy a Node.js Telegram Bot with Code Capsules, we’ve provided an example bot which you can find on the [Code Capsules GitHub repository](https://github.com/codecapsules-io/polling-nodejs-telegram-echobot).

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


## Add Environment Variables

Once the build is complete, you have to add the `BOT_TOKEN` environment variable by navigating to the "Config" tab and adding it under the "Environment Variables" section.

### `BOT_TOKEN`

Assign the `BOT_TOKEN` variable the value of the access token you were given by the BotFather when you registered the bot.

![Add a `BOT_TOKEN` Environment Variable](../assets/deployment/telegram/add-bot-token-env-var.png)

Confirm your changes by clicking on "Save", then restart your Capsule by toggling the radio button in the top right off and on again.

## Chat with the Bot

The bot will be able to respond to messages after restarting the Capsule. When this is done, search for your bot on Telegram using the username you assigned it and start a chat with it. The bot has been programmed to respond to `/start` and echo any messages you send it.

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](/docs/deployment/).
