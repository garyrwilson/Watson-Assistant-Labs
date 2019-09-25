# _**Watson Assistant Lab 2**_: Chatbot Integrations
In this lab we'll build a web-hosted chat widget that will allow you to test the _**Watson Assistant**_ _skill_ you've just created in a web environment. We'll then connect your _skill_ to a third party chat service **(Slack)** to show how you can easily run your chatbot within other applications, and we'll finish by creating a live, publicly available web-hosted version of your chatbot.

## Requirements
- Slack account
- Successful completion of [Lab 1: Cognitive Chatbot Basics](../1-Basics).

## Agenda
- Create an _**Assistant**_ for your skill
- Create a _**Preview Link**_: a web widget to test your chatbot
- Build a Slack bot
- Setup a live web-hosted chatbot

## Create an _**Assistant**_ for your skill
An _**assistant**_ is the user-facing component of _**Watson Assistant**_ that manages the flow of information between your _skills_ and your users. _Assistants_ also allow you to create _**integrations**_ that publish your chatbot to the channels your customers will typically go to for help, e.g. _Slack_, _Facebook Messenger_, _Wordpress_, etc.

**(1)** Select `IBM Watson Assistant` from the very top left of the screen, then from the `Assistants` screen, hit `Create assistant`.

![](./images/01-create-assistant.jpg)

**(2)** Add a **Name** and **Description** for your _Assistant_, and select `Create assistant`.

![](./images/02-add-assistant.jpg)

**(3)** From here Select `Add Dialog Skill` and select your `Phone Advisor` skill.

![](./images/03-add-dialog-skill.jpg)

![](./images/04-add-existing-skill.jpg)

**(4)** Your _Assistant_ should now look like this:

![](./images/05-skill-added.jpg)

## Create a _**Preview Link**_: a web widget to test your chatbot
The first _integration_ we'll create for our chatbot is a _**Preview Link**_. Actually, when you create an _assistant_, a test web site is provisioned for you _automatically_. It has a simple chat widget interface that you can use to interact with your chatbot for testing purposes. You can also share the URL to this IBM-branded site with your team members.

**(1)** Select the `Preview Link` that has already been created from `Integrations`:

![](./images/06-select-preview-link.jpg)

**(2)** You can change the **Name** and **Description** of the _Preview Link_ if you like. For now though, just click the URL under **Try it out and share the link**.

![](./images/07-select-preview-url.jpg)

**(3)** You'll then be taken to a web page where your chatbot is hosted within a widget:

![](./images/08-select-preview-init.jpg)

**(4)** That's it! You have a shareable web-based chatbot you can use to externally test your _skill_. Try some of the _dialog_ you've already created - you'll see that the widget handles both the text and image responses automatically.

![](./images/09-preview-test1.jpg)
![](./images/10-preview-test2.jpg)
![](./images/11-preview-test3.jpg)
![](./images/12-preview-test4.jpg)

## Build a Slack bot
So now you've built and tested your chatbot within the _**Watson Assistant**_ tooling, and also tested it using an automatically generated web widget. Now let's work on creating live, production versions of our chatbot.

_**Watson Assistant**_ provides an API for you to be able to use a chatbot within _a custom-built application_, as well as simple ways to integrate to popular messaging platforms. We'll do the latter here, and set up our chatbot to be accessible using the _Slack_ platform.

For this part of the lab, you will need a _Slack_ userid connected to a _Slack_ workspace **that allows you to create applications**. You can either use your one of your own organisation's _Slack_ workspaces for this, [create one yourself](https://get.slack.help/hc/en-us/articles/206845317-Create-a-Slack-workspace), or one will be provided for you if you are part of a guided lab session.

**(1)** Select `Add integration`, then `Slack` from the list of available built-in integrations.

![](./images/13-add-integration.jpg)

![](./images/14-select-slack.jpg)

**(2)** At this point you'll be presented with a form that will step you through the creation of a _Slack_ application, including the fields that need to be completed to allow your _**Watson Assistant**_ _skill_ to communicate with your _Slack_ bot.

![](./images/15-create-slack-app.jpg)

When you click one of the links in _Step 1_ and are in logged into _Slack_, select `Create New App`:

![](./images/16-slack-your-apps.jpg)

Name your app something like `PhoneAdvisorXXX`, where _XXX_ are your initials. When you hit `Create App` you'll see a configuration page for your _Slack_ application.

![](./images/17-name-slack-app.jpg)

![](./images/18-slack-app-config.jpg)

**(3)** Go back to _**Watson Assistant**_, and follow the steps to complete _Step 1_ in configuring your _Slack_ application.

![](./images/19-slack-watson-step1.jpg)

![](./images/20-slack-add-bot-user.jpg)

**(4)** Complete _Step 2_ in _**Watson Assistant**_ to connect _Slack_ to your chatbot:

![](./images/21-slack-watson-step2.jpg)

![](./images/22-slack-app-credentials.jpg)

![](./images/23-slack-app-oauth.jpg)

**(5)** Follow _Step 3_ in _**Watson Assistant**_ to configure your _Slack_ bot. After you hit `Generate request URL`, copy the URL presented into your _Slack_ app configuration as directed.

![](./images/24-slack-watson-request-URL.jpg)

![](./images/25-slack-watson-gen-URL.jpg)

![](./images/26-slack-event-sub.jpg)

**(6)** Finally, complete _Step 4_ in _**Watson Assistant**_. Make sure you **do complete the optional part 3 here**, as this will enable images to be displayed in our _Slack_ bot.

![](./images/27-slack-watson-step4.jpg)

![](./images/28-slack-bot-events.jpg)

![](./images/29-slack-interactive-components.jpg)

(7) Back in _**Watson Assistant**_ you can `Close` the _Slack_ Integration page. Now go to _Slack_ and test your live chatbot.

Select your app and try a few of your dialog conversations - it should work exactly as your test widget in _**Watson Assistant**_.

If you don't see your app, then you can add it to the _Slack_ workspace by using the **+** icon next to **Apps**.

![](./images/30-slack-demo.jpg)

## Setup a live web-hosted chatbot
As well as using _**Watson Assistant**_ _integrations_, you can build your own application that uses the chatbot logic you've created. This typically does require some web or mobile development skills. Fortunately, the _IBM iLab team_ has created a _**Chatbot Platform**_ application, that can create a live, customisable, publicly-available web-based chatbot for you, without the need for these skills.

**(1)** Launch the _Chatbot Platform_ from [here](http://chatbot-platform.eu-gb.mybluemix.net), and register for access. Once you've successfully registered, you will be taken to the Demo Dashboard.

**(2)** Hit `Create New` and select `Empty demo`:

![](./images/31-chatbot-platform-new.jpg)

![](./images/32-chatbot-platform-empty.jpg)

Now enter a name for your chatbot (e.g. `Phone Advisor`) and hit `Create`.

**(3)** From here we can link to our _**Watson Assistant**_ _skill_, and customise the look and feel of the web interface to our chatbot that we are about to create.

Start by editing the **Chatbot header** and **Chatbot subheader** fields. This is the text that will appear on the webpage as the title(s) of your chatbot. You can also use the **Choose colorscheme** dropdown menu here to change the basic look of your webpage.

![](./images/33-chatbot-platform-general.jpg)

**(4)** Next you'll need security credentials from _**Watson Assistant**_ in order to connect your chatbot skill to the web application that will be generated for you.

From the `Manage` screen within your _**Watson Assistant**_ service in _**IBM Cloud**_, you will need the `URL` and the `API key`. You can copy the API Key by selecting the `Copy to Clipboard!` icon.

![](./images/34-view-api-details.jpg)

In the _Chatbot Platform_:
- select `Watson Assistant` from the sidebar
- copy the URL (1) from your _**Watson Assistant**_ credentials in IBM Cloud, and paste it to the `Url` field
- copy the `API Key` (2) from your _**Watson Assistant**_ credentials into the `Apikey` field

Now hit `Save`.

![](./images/35-chatbot-platform-apikey.jpg)

**(5)** At this point the _Chatbot Platform_ application connects to _**Watson Assistant**_ and display a list of the _skills_ you've created, in a pulldown menu under **Workspace name**. If you had created multiple skills you would select the one you want to use here. As we've only created one so far, it's automatically selected:

![](./images/36-chatbot-platform-workspace.jpg)

**(6)** Finally, click the `Sharing & Security` menu option. Here you'll see a live link to your web chatbot. If you want a customised link then add some text here - it will be appended to the link URL. Also here, you can enter and save a password that will be used to login to your chatbot app.

![](./images/37-chatbot-platform-sharing.jpg)

**(7)** Now click the URL link, enter the password you've set, and you'll see your web-based chatbot! Test it out with your existing _dialog_ as before.

![](./images/38-live-web-chatbot.jpg)

## Summary
Well done! You've now built a basic chatbot that uses three user interfaces!

Next you should go to [Lab 3: Enhancing Your Chatbot with Advanced Watson Assistant Features](../3-Advanced) to further build out and enhance your chatbot by using more _**Watson Assistant**_ functionality.
