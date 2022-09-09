---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
---
### Prerequisites
* A Twitter account 

Before you can use your Twitter account in a logic app, you must authorize the logic app to connect to your Twitter account. Fortunately, you can do this easily from within your logic app on the Azure Portal. 

Here are the steps to authorize your logic app to connect to your Twitter account:

1. To create a connection to Twitter, in the logic app designer, select **Show Microsoft managed APIs** in the drop down list then enter *Twitter* in the search box. Select the trigger or action you'll like to use:  
   ![Twitter connection image 0](./media/connectors-create-api-twitter/twitter-0.png)
2. If you haven't created any connections to Twitter before, you'll get prompted to provide your Twitter credentials. These credentials will be used to authorize your logic app to connect to, and access your Twitter account's data:  
   ![Twitter connection image 1](./media/connectors-create-api-twitter/twitter-1.png)  
3. Provide your Twitter user name and password to authorize your logic app:  
   ![Twitter connection image 2](./media/connectors-create-api-twitter/twitter-2.png)  
4. Confirm your authorization:  
   ![Twitter connection image 3](./media/connectors-create-api-twitter/twitter-3.png)  
5. Notice the connection has been created and you are now free to proceed with the other steps in your logic app:  
   ![Twitter connection image 4](./media/connectors-create-api-twitter/twitter-4.png)

