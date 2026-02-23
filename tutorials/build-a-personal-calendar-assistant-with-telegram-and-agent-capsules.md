---
description: >-
  Build an AI-powered calendar assistant that manages your Google Calendar
  through natural Telegram conversations, deployed on Code Capsules.
cover: .gitbook/assets/calendar-assistant-cover.jpg
coverY: 0
coverHeight: 421
layout:
  width: default
  cover:
    visible: true
    size: full
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
---

# Build a Personal Calendar Assistant with Telegram and Agent Capsules

Managing your calendar through multiple apps is tedious. You check Google Calendar on desktop, get notifications on mobile, and manually coordinate across platforms. What if you could schedule meetings, check availability, and get reminders through a single Telegram chat?

In this tutorial, you'll build a personal calendar assistant that handles your Google Calendar through natural conversation on Telegram. The assistant understands requests like "Schedule a team meeting tomorrow at 2pm" or "What's on my calendar this week?" without requiring you to manage servers, databases, or scaling infrastructure.

<figure><img src=".gitbook/assets/telegram-agent-bot-add-event.png" alt="The finished calendar assistant responding to a natural language request to schedule a meeting"><figcaption><p>The calendar assistant you'll build in this tutorial</p></figcaption></figure>

### Agentic Infrastructure

Deploying AI agents to production requires managing extensive infrastructure:

* Provision servers like EC2 instances and configure load balancers
* Deploy and maintain vector databases (Pinecone, Weaviate, or Redis)
* Configure SSL/TLS certificates and API authentication
* Manage secrets securely
* Add monitoring and logging for observability and error tracking
* Configure autoscaling policies and resource limits

Code Capsules eliminates this burden. The platform handles infrastructure, databases, security, and scaling automatically, letting you focus on building Agent tools and features.

### Prerequisites

To follow this tutorial, you need:

* A [GitHub account](https://github.com/) and [Git](https://git-scm.com/) installed
* A [Code Capsules](https://codecapsules.io/) account
* An LLM API key. For example: [Anthropic](https://www.anthropic.com/) or [OpenAI](https://openai.com/index/openai-api/)

### Set Up a Redis Capsule

The agents in this tutorial need a vector database. For simplicity, we use a Redis Capsule.

**Note:** This setup is not recommended for production. Redis is designed for temporary information and caching. For production deployments, consider dedicated vector databases like [Pinecone](https://www.pinecone.io/), [Weaviate](https://weaviate.io/), or [Qdrant](https://qdrant.tech/).

Follow the existing [guide to creating a Redis Capsule](https://app.gitbook.com/s/xjp0G5hHSJs8nyv5Z5g7/database/redis).

Once you've created the Redis Capsule, copy the connection string from the **Capsule Details** page.

<figure><img src=".gitbook/assets/telegram-agent-redis-connection-string.png" alt=""><figcaption></figcaption></figure>

### Configure the Telegram Agent

To set up the Telegram agent, go to the [Telegram calendar agent](https://github.com/codecapsules-io/ai-telegram-bot-calendar-agent-template) GitHub repository, and click **Use this template** to create a copy in your own GitHub account.

Then, deploy the Agent Capsule:

1. In your Code Capsules dashboard, click **New Capsule** and select **Agent Capsule**.
2. Choose your Team and Space.
3. Connect your GitHub account and select the repository you just created.
4. Click **Create Capsule**.

For more detailed instructions, see the [Agent Capsule deployment guide](https://app.gitbook.com/s/oyCI3rJYfUxA3cJhHZbu/agent-capsule/deploy).

#### Create a Telegram Bot

To create a Telegram Bot, open the [Telegram](https://telegram.org/) application and search for [BotFather](https://t.me/botfather). Start a conversation with BotFather and select `/newbot`. When prompted, give the bot a name, then a username. Once it is created, the bot provides you with a token. Copy this token and save it for later.

<figure><img src=".gitbook/assets/telegram-agent-botfather-create-bot.png" alt=""><figcaption></figcaption></figure>

To verify the setup, make sure you can start a conversation with the bot (it won't respond yet).

Now that you have the token, go to your Agent Capsule **Config** page and add the following variables:

```bash
TELEGRAM_BOT_TOKEN=your_bot_token
REDIS_URL=your_copied_connection_string
```

Paste the Telegram bot token and the Redis connection string as new **Environment Variables**.

<figure><img src=".gitbook/assets/telegram-agent-capsule-env-vars.png" alt=""><figcaption></figcaption></figure>

The Telegram agent is now ready. Let's configure the calendar capabilities.

#### Configure the Google Calendar API

To ensure the agent can access your calendar, you must configure Google Cloud to integrate with it.

Visit the Google Calendar API [quickstart](https://developers.google.com/workspace/calendar/api/quickstart/js). Scroll down and click the **Enable the API** button.

<figure><img src=".gitbook/assets/telegram-agent-google-calendar-enable-api.png" alt=""><figcaption></figcaption></figure>

The button redirects you to the Google Console platform page to confirm your project and enable the API.

<figure><img src=".gitbook/assets/telegram-agent-google-calendar-api-enabled.png" alt=""><figcaption></figcaption></figure>

After that, navigate to the **Configure the OAuth consent screen** section in the Google Calendar configuration doc. Click the **Go to Branding** button, and follow the steps in the Google documentation. If you can't select **Internal** for the audience, you can select **External**, but you'll have to add test users with the Google email address you're using.

<figure><img src=".gitbook/assets/telegram-agent-google-oauth-consent-screen.png" alt=""><figcaption></figcaption></figure>

Then, authorize your web application credentials by clicking the **Go to Clients** button.

<figure><img src=".gitbook/assets/telegram-agent-google-oauth-clients.png" alt=""><figcaption></figcaption></figure>

When doing so, set the redirect URI to the URL of the Calendar Agent and specify the endpoint `/api/calendar/auth/callback`. To find the public URL of your Calendar Agent, open your Agent Capsule dashboard, navigate to the **Details** tab, and copy the **Public URL**.

<figure><img src=".gitbook/assets/telegram-agent-capsule-public-uri.png" alt=""><figcaption></figcaption></figure>

For example, the full URL would have the following structure:

```
https://agent-capsule-123.ovh-test.ccdns.co/api/calendar/auth/callback
```

After completing setup, Google provides you with credentials such as the client ID and the client secret. Add these in the Agent Capsule dashboard **Config** tab as follows:

```bash
GOOGLE_CALENDAR_CLIENT_ID=your_client_id
GOOGLE_CALENDAR_CLIENT_SECRET=your_client_secret
```

Your environment variables configuration should look as follows:

<figure><img src=".gitbook/assets/telegram-agent-capsule-full-env-vars.png" alt=""><figcaption></figcaption></figure>

Test the calendar configuration by clicking on the **Chat** tab, where you can make the following request:

```txt
Get the list of events in the calendar for February in the first 3 days.
```

The Agent should give you a link to click to authorize access to the Calendar API.

<figure><img src=".gitbook/assets/telegram-agent-chat-auth-link.png" alt=""><figcaption></figcaption></figure>

After granting access, you should see the following screen:

<figure><img src=".gitbook/assets/telegram-agent-auth-success.png" alt=""><figcaption></figcaption></figure>

After the validation, return to the **Chat** and tell the agent to proceed. You should receive a similar response to this:

<figure><img src=".gitbook/assets/telegram-agent-chat-calendar-response.png" alt=""><figcaption></figcaption></figure>

### Test the Integration

To test the integration, go to Telegram and start a conversation with the bot. Ask the bot to retrieve your calendar events for a particular period. If the bot prompts you for login or authorization, click on the link and grant it access.

<figure><img src=".gitbook/assets/telegram-agent-bot-auth-prompt.png" alt=""><figcaption></figcaption></figure>

After granting access, remind the Agent to continue.

<figure><img src=".gitbook/assets/telegram-agent-bot-calendar-list.png" alt=""><figcaption></figcaption></figure>

You can also add events to the calendar.

<figure><img src=".gitbook/assets/telegram-agent-bot-add-event.png" alt=""><figcaption></figcaption></figure>

### Conclusion

In this tutorial, you built a Telegram bot that acts as an interface to a Code Capsules Agent with Google Calendar capabilities. The Agent uses an LLM to understand your requests and interact with your calendar through natural language.

This same pattern can enable you to build interesting integrations like:

* **Task Management Bots:** Connect [Todoist](https://todoist.com/), [Asana](https://asana.com/), or [Jira](https://www.atlassian.com/software/jira) to manage tasks through chat.
* **More Personal Assistants:** Combine multiple services like email, calendar, and notes in one conversational interface.
* **Customer Support Bots:** Integrate with CRM systems and knowledge bases to handle support queries.
* **Data Analysis Tools:** Query databases or analytics platforms using natural language.

The Agent Capsule architecture makes it easy to add new tools and capabilities without managing infrastructure. You can extend your bot by creating new tools that call external APIs, process data, and integrate with other services.

If you're interested in learning more about building with Agent Capsules, check out the [Code Capsules documentation](https://app.gitbook.com/s/oyCI3rJYfUxA3cJhHZbu/storage-capsule).
