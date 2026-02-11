---
description: A guide to deploying a WhatsApp Bot from GitHub.
---

# WhatsApp Bot

Deploy a WhatsApp Bot and learn how to host backend code on Code Capsules.

## Setup

Code Capsules connects to GitHub repositories to deploy applications. To follow this guide, you'll need a [Code Capsules](https://codecapsules.io/) account, a [GitHub](https://github.com/) account, and a [Twilio](https://www.twilio.com/try-twilio) account.

To demonstrate how to deploy a WhatsApp Bot with Code Capsules, we've provided an example bot, which you can find on the [Code Capsules GitHub repository](https://github.com/codecapsules-io/whatsapp-echobot).

Sign in to GitHub, and fork the example bot repository by clicking **Fork** at the top-right of your screen and selecting your GitHub account as the destination.

## Create a Space for Your Bot

Log in to your Code Capsules account and navigate to the **Spaces** tab. Once there, click the yellow `+` icon on the bottom left of the screen to add a new Space.

Follow the prompts, choosing your region and giving your Space a name, then click **Create Space**.

![Create a Space](/broken/files/c1RKSsytKv66iCGB7Ln3)

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

![Build Logs](/broken/files/m10b9QRjcH8SGo8JjPp9)

## Create a Twilio Sandbox

The Twilio Sandbox provides a development environment to access the WhatsApp API. Sign up for a [Twilio account](https://www.twilio.com/try-twilio) to use a sandbox that allows you to test your bot in real-time. After you've logged into your Twilio account, navigate to the [console](https://www.twilio.com/console/sms/whatsapp/sandbox) page to configure your WhatsApp sandbox settings.

1.  Go to your capsule's **Details** tab and copy your bot's domain under the **URL** section.

    <figure><img src="../../.gitbook/assets/backend-url.png" alt=""><figcaption></figcaption></figure>
2.  Head back to your Twilio console and paste the domain in the **When a Message Comes In** field and append `/bot` to the end of it. Make sure the method is set to _HTTP Post_.

    <figure><img src="../../.gitbook/assets/sandbox-config.png" alt=""><figcaption></figcaption></figure>
3. Scroll down to the bottom of the page and click **Save**.
4.  Under the **Sandbox Participants** section, you will find the WhatsApp number for your sandbox and a code to join it that starts with **join**. Send this code to the displayed WhatsApp number to add your personal number as a sandbox participant.

    <figure><img src="../../.gitbook/assets/sandbox-participants.png" alt=""><figcaption></figcaption></figure>

## Chat with the Bot

The bot will now be able to respond to your messages after sending the join code. Try it, and the bot should echo any message you send it.

If you’d like to deploy another application in a different language or framework, take a look at our other [deployment guides](../../).
