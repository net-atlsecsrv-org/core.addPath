---
title: What is Personalizer?
description: Personalizer is a cloud-based service that allows you to choose the best experience to show to your users, learning from their real-time behavior.
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalizer, Azure personalizer, machine learning 
---

# What is Personalizer?

Azure Personalizer is a cloud-based service that helps your applications choose the best content item to show your users. You can use the Personalizer service to determine what product to suggest to shoppers or to figure out the optimal position for an advertisement. After the content is shown to the user, the system monitors real-time user behavior and reports a reward score back to the Personalizer service. This ensures continuous improvement of the machine learning model, and Personalizer's ability to select the best content item based on the contextual information it receives.

> [!TIP]
> Content is any unit of information, such as text, images, URL, emails, or anything else that you want to select from and show to your users.

Before you get started, feel free to try out [Personalizer with this interactive demo](https://personalizationdemo.azurewebsites.net/).

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## How does Personalizer select the best content item?

Personalizer uses **reinforcement learning** to select the best item (_action_) based on collective behavior and reward scores across all users. Actions are the content items, such as news articles, specific movies, or products.

The **Rank** call takes the action item, along with features of the action, and context features to select the top action item:

* **Actions with features** - content items with features specific to each item
* **Context features** - features of your users, their context or their environment when using your app

The Rank call returns the ID of which content item, __action__, to show to the user, in the **Reward Action ID** field.

The __action__ shown to the user is chosen with machine learning models, that try to maximize the total amount of rewards over time.

### Sample scenarios

Let's take a look at a few scenarios where Personalizer can be used to select the best content to render for a user.

|Content type|Actions (with features)|Context features|Returned Reward Action ID<br>(display this content)|
|--|--|--|--|
|News list|a. `The president...` (national, politics, [text])<br>b. `Premier League ...` (global, sports, [text, image, video])<br> c. `Hurricane in the ...` (regional, weather, [text,image]|Device news is read from<br>Month, or season<br>|a `The president...`|
|Movies list|1. `Star Wars` (1977, [action, adventure, fantasy], George Lucas)<br>2. `Hoop Dreams` (1994, [documentary, sports], Steve James<br>3. `Casablanca` (1942, [romance, drama, war], Michael Curtiz)|Device movie is watched from<br>screen size<br>Type of user<br>|3. `Casablanca`|
|Products list|i. `Product A` (3 kg, $$$$, deliver in 24 hours)<br>ii. `Product B` (20 kg, $$, 2 week shipping with customs)<br>iii. `Product C` (3 kg, $$$, delivery in 48 hours)|Device shopping  is read from<br>Spending tier of user<br>Month, or season|ii. `Product B`|

Personalizer used reinforcement learning to select the single best action, known as _reward action ID_. The machine learning model uses: 

* A trained model - information previously received from the personalize service used to improve the machine learning model
* Current data - specific actions with features and context features

## When to use Personalizer

Personalizer's **Rank** [API](https://go.microsoft.com/fwlink/?linkid=2092082) is called each time your application presents content. This is known as an **event**, noted with an _event ID_.

Personalizer's **Reward** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) can be called in real-time or delayed to better fit your infrastructure. You determine the reward score based on your business needs. The reward score is between 0 and 1. That can be a single value such as 1 for good, and 0 for bad, or a number produced by an algorithm you create considering your business goals and metrics.

## Content requirements

Use Personalizer when your content:

* Has a limited set of items (max of ~50) to select from. If you have a larger list, [use a recommendation engine](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) to reduce the list down to 50 items.
* Has information describing the content you want ranked: _actions with features_ and _context features_.
* Has a minimum of ~1k/day content-related events for Personalizer to be effective. If Personalizer doesn't receive the minimum traffic required, the service takes longer to determine the single best content item.

Since Personalizer uses collective information in near real-time to return the single best content item, the service doesn't:
* Persist and manage user profile information
* Log individual users' preferences or history
* Require cleaned and labeled content

## How to design for and implement Personalizer

1. [Design](concepts-features.md) and plan for content, **_actions_**, and **_context_**. Determine the reward algorithm for the **_reward_** score.
1. Each [Personalizer Resource](how-to-settings.md) you create is considered one Learning Loop. The loop will receive the both the Rank and Reward calls for that content or user experience.

    |Resource type| Purpose|
    |--|--|
    |[Apprentice mode](concept-apprentice-mode.md) `E0`|Train the Personalizer model without impacting your existing application, then deploy to Online learning behavior to a production environment|
    |Standard, `S0`|Online learning behavior in a production environment|
    |Free, `F0`| Try Online learning behavior in a non-production environment|

1. Add Personalizer to your application, website, or system:
    1. Add a **Rank** call to Personalizer in your application, website, or system to determine best, single _content_ item before the content is shown to the user.
    1. Display best, single _content_ item, which is the returned _reward action ID_, to user.
    1. Apply _business logic_ to collected information about how the user behaved, to determine the **reward** score, such as:

    |Behavior|Calculated reward score|
    |--|--|
    |User selected best, single _content_ item (reward action ID)|**1**|
    |User selected other content|**0**|
    |User paused, scrolling around indecisively, before selecting best, single _content_ item (reward action ID)|**0.5**|

    1. Add a **Reward** call sending a reward score between 0 and 1
        * Immediately after showing your content
        * Or sometime later in an offline system
    1. [Evaluate your loop](concepts-offline-evaluation.md) with an offline evaluation after a period of use. An offline evaluation allows you to test and assess the effectiveness of the Personalizer Service without changing your code or affecting user experience.

## Complete a quickstart

We offer quickstarts in C#, JavaScript, and Python. Each quickstart is designed to teach you basic design patterns, and have you running code in less than 10 minutes. 

* [Quickstart: How to use the Personalizer client library](sdk-learning-loop.md)

After you've had a chance to get started with the Personalizer service, try our tutorials and learn how to use Personalizer in web applications, chat bots, or an Azure Notebook.

* [Tutorial: Use Personalizer in a .NET web app](tutorial-use-personalizer-web-app.md)
* [Tutorial: Use Personalizer in a .NET chat bot](tutorial-use-personalizer-chat-bot.md)
* [Tutorial: Use Personalizer in an Azure Notebook](tutorial-use-azure-notebook-generate-loop-data.md)

## Reference 

* [Personalizer C#/.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/personalizer?view=azure-dotnet)
* [Personalizer Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/personalizer/v1.0/personalizer)
* [Personalizer JavaScript SDK](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)
* [Personalizer Python SDK](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/personalizer?view=azure-python)
* [REST APIs](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## Next steps

> [!div class="nextstepaction"]
> [How Personalizer works](how-personalizer-works.md)
> [What is Reinforcement Learning?](concepts-reinforcement-learning.md)
