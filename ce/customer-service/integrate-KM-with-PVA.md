---
title: "Integrate knowledge management with Power Virtual Agents bot| MicrosoftDocs"
description: "Learn about the integration of knowledge management with Power Virtual Agents bot."
author: meghanalanka
ms.author: v-mlanka
manager: shujoshi
ms.date: 05/11/2021
ms.topic: article
ms.service: dynamics-365-customerservice
---

# Integrate knowledge management with Power Virtual Agents bot

Integrate a Power Virtual Agents bot to assist in knowledge management by performing the following steps.

1. [Create and publish knowledge articles to the Power Apps portal](#create-and-publish-knowledge-articles).
2. [Create a topic for a Power Virtual Agents bot and add an action](#create-a-topic-for-a-power-virtual-agents-bot-and-add-an-action).
3. [Use the Power Automate template to add the solution to the Power Virtual Agents bot](#use-power-automate-template-to-add-the-solution-to-the-flow).
4. [Test the flow to publish and share the Power Virtual Agents bot](#test-and-publish-the-power-virtual-agents-bot).

## Create and publish knowledge articles

Set up your Power Apps portal before you integrate knowledge management with a Power Virtual Agents bot. More information: [Configure Knowledge Management](set-up-knowledge-management-embedded-knowledge-search.md).

You can create and manage knowledge articles by marking the **Internal** field to **No**. For more information on managing knowledge articles, see [Create and manage knowledge articles](customer-service-hub-user-guide-knowledge-article.md).

## Create a topic for a Power Virtual Agents bot and add an action

The system fallback topic can help agents present answers to customers when the knowledge search doesn't provide relevant knowledge articles. You can create the fallback topic and add an action. The action allows the fallback topic to be displayed as a message to agents.

1. Sign in to Power Virtual Agents.
2. Create a **New bot**. More information: [Create and delete bots](/power-virtual-agents/authoring-first-bot).

You can select **Topics** to see the knowledge articles and topics that are provided by the bot.

3. Select **Settings** and then **System Fallback**.
4. Select **Add**.
5. After the topic is added, select **Go to Fallback topic** to author the topic.
6. On the authoring canvas, delete the **Escalate** item by selecting **Options.**

    > [!div class="mx-imgBorder"]
    > ![Author a fallback topic](media/escalate-fallback-KM-PVA.png "Author a fallback topic")

7. Select the connector flowing to the message box, select **Call an action**, and then **Create a flow**.

    > [!div class="mx-imgBorder"]
    > ![Create a flow to call an action](media/create-a-flow-KM-PVA.png "Create a flow to call an action")

The Power Automate application is opened on a new tab where you will configure the template settings.

## Use Power Automate Template to add the solution to the flow

You can use the Power Automate template that you created to connect to the knowledge base by performing the following steps.

1. In the Power Automate page, select **Templates**.
 
    > [!NOTE]
    > In the pop-up box that appears, select **Leave** and exit the current browser page.

2. On the templates page, search for the **Generate answers from Dataverse knowledge articles to Power Virtual Agent** template. 

    > [!div class="mx-imgBorder"]
    > ![Create a flow](media/template-KM-PVA.png "Create a flow")

3. Select **Continue**. You will be redirected to the Power Automate flow template.
4. Save the template and then select **Solutions**. 
5. Select **New solution**.
6. Enter a display name. The list of solutions includes every solution in your organization. Choose a naming convention that helps you filter to just your solutions. For example, you might prefix your email to your solution name: *johndoe-power-virtual-agent-knowledgesearch-fallback*.
7. Select your publisher from the list of choices.
8. Accept the default values for name and version.
9. Select **Create** to finish the process.
 
     > [!div class="mx-imgBorder"]
     > ![Add a solution](media/add-solution-KM-PVA.png "Add a solution")

10. In the list of solutions, select the solution you just created. It should be at the top of the list. If it isn't, search by your email address, which is part of the solution name.
11. In the solution, select **Add existing**, and then select **Cloud Flow** from the list.
12. Find your flow from the **Outside solutions** list, and then select **Add** to finish the process. If there are several flows, look at the **Modified** column to find the most recent flow.

### Add the solution's flow to Power Virtual Agents bot

1. Open the browser with the authoring canvas.
2. To insert a new step in the flow, above the **Message** action box, select the connector, and then select **Call an action**.
3. From the flow pop-up window, select the new flow named **Generate answer from Dataverse knowledge articles for Power Virtual Agent**. The new action appears in the flow.
4. To correctly set the input variable to the action, select **Select a variable**, and then select **bot.UnrecognizedTriggerPhrase**.

     > [!div class="mx-imgBorder"]
     > ![Add flow to Power Virtual Agents](media/bot-KM-PVA.png "Add flow to Power Virtual Agents")

5. To return the knowledge article search results to the bot, there are a few steps that you can perform.
    
    - To correctly set the output variable to the **Generate answer from Dataverse knowledge articles for Power Virtual Agent** action, select **UnrecognizedTriggerPhrase**, from the **Message** action box. Select the drop-down box next to {x} to insert a variable. Select  **textResult** which has the article result in text format. This will render the whole article content as text in the message. 
    
    - Or use thumbnail cards to show the article. More information: [Respond with cards](/composer/how-to-send-cards#thumbnailcard).
    
    - Or use the sample code to render in adaptive cards.
    

    
#kbcardjson()
```
- 
{
  "type": "AdaptiveCard",
    "body": [
        {
            "type": "ColumnSet",
            "columns": [
                {
                    "type": "Column",
                    "items": [
                        {
                            "type": "Image",
                            "height": "35px",
                            "url": "https://th.bing.com/th/id/R4fbade037f8209666b06aa22641708fc?rik=ya%2bpnPJ41EFFMg&riu=http%3a%2f%2fmolnar-institute.com%2ffileadmin%2f_processed_%2fcsm_KnowledgeManagement_icon_forWebsite_bcda89676b.png&ehk=J%2bz2G15xtbJgd7ssSXo7X2q7LDLaMGl7EuGUSQU6P2s%3d&risl=&pid=ImgRaw",
                            "size": "Small"
                        }
                    ],
                    "width": "auto"
                },
                {
                    "type": "Column",
                    "items": [
                        {
                            "type": "TextBlock",
                            "weight": "Bolder",
                            "text": "${dialog.currentarticle.title}",
                            "wrap": true,
                            "size": "Large"
                        },
                        {
                            "type": "TextBlock",
                            "spacing": "None",
                            "text": "Last modified on ${dialog.currentarticle.modifiedon}",
                            "isSubtle": true,
                            "wrap": true
                        }
                    ],
                    "width": "stretch"
                }
            ]
        },
        {
            "type": "TextBlock",
            "text": "${json(virtualagent.jsonResult).articles[0].description}",
            "wrap": true,
            "size": "medium"
        }
    ],
    "actions": [
        {
            "type": "Action.OpenUrl",
            "title": "View article in Dynamics App",
            "url": "${dialog.currentarticle.articleurl}"
        }
    ],
    "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
    "version": "1.0"
}
```

## Test and publish the Power Virtual Agents bot

After you have the fallback topic saved, you can test your bot by typing some queries. If an existing topic can't answer the question, the topic that you created is displayed as a message. After publishing the Power Virtual Agents bot, you can also share your bot. 

  > [!div class="mx-imgBorder"]
  > ![Test flow](media/test-flow-KM-PVA.png "Test flow")

To publish the Power Virtual agent bot, select **Publish**, and then look for the demo link that you will find on the same page.

You can share the demo by configuring it as a channel. 

1. Select **Manage** > **Channel**.
2. Select **Demo website** from the channels list.
3. Copy the link, and select **Save**.

### See also

[Create and edit topics](/power-virtual-agents/authoring-create-edit-topics)
