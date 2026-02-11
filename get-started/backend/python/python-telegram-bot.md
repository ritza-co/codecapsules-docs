---
description: A guide to deploying a Python Telegram Bot from GitHub.
---

# Python Telegram Bot

_A video for this guide can be found_ [_here_](https://www.youtube.com/watch?v=z-K9rVfhd5c\&list=PLoEGujFfB4nakOY7ifjldejFZBREfn3Zd\&index=1) _if you prefer watching to reading._

Deploy a Python Telegram Bot and learn how to host backend code on Code Capsules.

## Register the Bot

You'll need a Telegram user account before you can create a Telegram bot. Head over to Telegram and create an account if you don't already have one.

When you've signed in to Telegram, search for **BotFather** (a bot for managing all other Telegram bots) and start a new chat with it. Follow the steps below to register a new bot with the BotFather:

1. Type `/start` and press send.
2. Type `/newbot` and press send.
3. Choose a name for your bot.
4. Choose a username for your bot that ends in **bot**.

The BotFather will respond with a message containing an access token for your newly created bot. This access token will allow our application to access the Telegram API and tell our bot what to do when receiving different messages from users.

To confirm that your bot was created successfully, search for the bot's username. You should be able to see it and start a conversation with it, although it won't respond as we haven't written the bot's logic yet.

## Setup

Code Capsules connects to GitHub repositories to deploy applications. To follow this guide, you'll need a [Code Capsules](https://codecapsules.io/) account and a [GitHub](https://github.com/) account.

To demonstrate how to deploy a Python Telegram Bot with Code Capsules, we've provided an example bot, which you can find on the [Code Capsules GitHub repository](https://github.com/codecapsules-io/python-telegram-echobot).

Sign in to GitHub, and fork the example bot repository by clicking **Fork** at the top-right of your screen and selecting your GitHub account as the destination.

## Create a Space for Your Bot

Log in to your Code Capsules account and navigate to the **Spaces** tab. Once there, click the yellow `+` icon on the bottom left of the screen to add a new Space.

Follow the prompts, choosing your region and giving your Space a name, then click **Create Space**.

![Create a Space](../../.gitbook/assets/space-name.png)

Example instructions to go with numbered annotations

1. Choose a Team — you can use a default **personal** Team if you're the only person working on this project, or a named Team if you're collaborating with others
2. This should remind you of the project, for example, **customer-api** or **notetaking-app**
3. Choose a country close to where most of your users will be

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

Code Capsules will automatically build your application when you've finished creating the Capsule.

Once your application is live, you can view the build log by selecting the **Deploy** tab and clicking the **View build log** link in the **Builds** section.

![Build Logs](../../.gitbook/assets/backend-capsule-build-logs.png)

## Add Environment Variables

Once the build is complete, you have to add `BOT_TOKEN` and `URL` environment variables on the **Config** tab under the **Environment Variables** section.

Assign the `BOT_TOKEN` variable the value of the access token you were given by the BotFather when you registered the bot.

![Add a BOT\_TOKEN Environment Variable](../../.gitbook/assets/bot-token-env-variable.png)

For the `URL` variable, set it to the value of your bot's domain. You can get it by clicking the **Live Website** link to the left of the capsule's toggle button and copying the URL in the new tab that opens. Paste the URL you copied in the value field for the `URL` environment variable (make sure the URL ends with a `/` or the webhook will not be valid).

![Add a URL Environment Variable](../../.gitbook/assets/url-env-variable.png)

Confirm your changes by clicking on **Save**, then restart your Capsule by toggling the radio button in the top right off and on again.

## Set Up Webhook

The next step is to set up a webhook for your bot. Do this by clicking the **Live Website** link at the top of the capsule's page. On the new tab that opens, add `/setwebhook` to the URL and press enter/return to visit the URL. If you see `webhook setup ok` then your bot is ready to chat!

## Chat with the Bot

The bot will be able to respond to messages after completing the above steps. When this is done, search for your bot on Telegram using the username you assigned it and start a chat with it. The bot has been programmed to respond to `/start` and echo any messages you send it.

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](../../).
