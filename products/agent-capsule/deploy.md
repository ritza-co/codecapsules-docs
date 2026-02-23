# Deploy

Deploy a configurable Agent Capsule on Code Capsules.

## Set up

To follow this guide, you'll need a [Code Capsules](https://codecapsules.io/) account.

## Create an Account with Code Capsules

Before creating your Agent Capsule, you'll need a Team and a Space. You can follow these guides to learn how to create [Teams](../../platform/teams/what-is-a-team.md) and [Spaces](../../platform/spaces/what-is-a-space.md).

If you already have a Team and Space set up, log in to your Code Capsules account. On the dashboard, click the yellow `+` on the bottom left of the screen then click "New Capsule".

![Create a Capsule](../.gitbook/assets/add-capsule.png)

## Create the Capsule

A [Capsule](../../platform/capsules/what-is-a-capsule.md) provides the server for hosting an application on Code Capsules.

To create an Agent Capsule first choose "AI Agent" as the Capsule type, as well as your Team, and Space.

<figure><img src="../.gitbook/assets/CreateAgentCapsule_Edited.png" alt="Create Agent Capsule"><figcaption><p>Create Agent Capsule</p></figcaption></figure>

Next choose your payment plan, or create a custom plan.

<figure><img src="../.gitbook/assets/CreateAgentScale_Edited.png" alt=""><figcaption><p>Choose Plan</p></figcaption></figure>

Code Capsules provides templates (see [templates.md](templates.md "mention")) as a baseline for creating your AI Agent. When creating the Agent Capsule, you can select if your codebase comes from a template repo. In this case, you will be asked to enter a provider, model, and API key for a public AI provider, such as Google Gemini or Anthropic. When registering with one of these providers, they should provide an API key to access their public API's. Once the capsule is created, the templates will make use of this information via environment variables, which are automatically set up for you upon capsule creation. You can learn more about how these templates make use of these environment variables [here](templates.md).

<figure><img src="../.gitbook/assets/CreateAgentConfig_Edited.png" alt=""><figcaption><p>Template Setup</p></figcaption></figure>

## Monitor Deployment

Code Capsules will automatically build and deploy your Agent Capsule. You can view the build log by selecting the "Build logs" tab to monitor the deployment progress.

<figure><img src="../.gitbook/assets/AgentBuildLogs_Edited.png" alt=""><figcaption><p>Build Logs</p></figcaption></figure>

Once the build is complete, use the Chat tab to interface with your newly running agent.

<figure><img src="../.gitbook/assets/AgentChatPage_Edited.png" alt=""><figcaption><p>Chat Tab</p></figcaption></figure>
