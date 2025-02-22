---
title: include file
description: include file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
---

You will need to install the Personalizer client library for Python to:
* Authenticate the quickstart example client with a Personalizer resource in Azure.
* Send context and action features to the Reward API, which will return the best action from the Personalizer model
* Send a reward score to the Rank API and train the Personalizer model.

[Reference documentation](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer) | [Library source code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-personalizer) | [Package (pypi)](https://pypi.org/project/azure-cognitiveservices-personalizer/) | [Quickstart code sample](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Once your Azure subscription is set up, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Create a Personalizer resource"  target="_blank">create a Personalizer resource </a> in the Azure portal to get your key and endpoint. After it deploys, click **Go to resource**.
    * You will need the key and endpoint from the created resource to connect your application to the Personalizer API. You'll paste your key and endpoint into the code below later in the quickstart.
    * You can use the free pricing tier (`F0`) to try the service, and upgrade to a paid tier for production at a later time.

## Setting Up

[!INCLUDE [Change model frequency](change-model-frequency.md)]

[!INCLUDE [Change reward wait time](change-reward-wait-time.md)]

### Install the client library

After installing Python, the client library can be installed with [pip](https://pypi.org/project/pip/):

```console
pip install azure-cognitiveservices-personalizer
```

### Create a new Python application

First, create a Python application that will both handle the toy example logic and call the Personalizer APIs. 

Create a new Python file and create variables for your resource's endpoint and subscription key.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```python
from azure.cognitiveservices.personalizer import PersonalizerClient
from azure.cognitiveservices.personalizer.models import RankableAction, RewardRequest, RankRequest
from msrest.authentication import CognitiveServicesCredentials

import datetime, json, os, time, uuid

key = "<paste-your-personalizer-key-here>"
endpoint = "<paste-your-personalizer-endpoint-here>"
```

## Object model

The Personalizer client is a [PersonalizerClient](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.personalizer_client.personalizerclient) object that authenticates to Azure using Microsoft.Rest.ServiceClientCredentials, which contains your key.

To ask for the single best item of the content, create a [RankRequest](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.models.rankrequest), then pass it to client.Rank method. The Rank method returns a RankResponse.

To send a reward score to Personalizer, set the event ID and the reward score (value) to send to the [Reward](/python/api/azure-cognitiveservices-personalizer/azure.cognitiveservices.personalizer.operations.events_operations.eventsoperations) method on the EventOperations class.

Determining the reward, in this quickstart is an easy task, however, this is on one of the most important considerations when planning your Personalizer architecture. In a production system, determining what factors impacts the [reward score](../concept-rewards.md) and how much can be complex. Furthermore, you may decide to change your reward scoring mechanism as your scenario or business needs evolve. 

## Code examples

These code snippets show you how to do the following with the Personalizer client library for Python:

* [Authenticate the client](#authenticate-the-client)
* [Rank API](#request-the-best-action)
* [Reward API](#send-a-reward)

## Authenticate the client

Instantiate the `PersonalizerClient` with your `key` and `endpoint` which you created earlier.

```python
# Instantiate a Personalizer client
client = PersonalizerClient(endpoint, CognitiveServicesCredentials(key))
```

## Get content choices represented as actions

Actions represent the content choices from which Personalizer can select the best content item. The following methods create a set of sample actions along with action features in your quickstart application. 

```python
def get_actions():
    action1 = RankableAction(id='pasta', features=[{"taste":"salty", "spice_level":"medium"},{"nutrition_level":5,"cuisine":"italian"}])
    action2 = RankableAction(id='ice cream', features=[{"taste":"sweet", "spice_level":"none"}, { "nutritional_level": 2 }])
    action3 = RankableAction(id='juice', features=[{"taste":"sweet", 'spice_level':'none'}, {'nutritional_level': 5}, {'drink':True}])
    action4 = RankableAction(id='salad', features=[{'taste':'salty', 'spice_level':'none'},{'nutritional_level': 2}])
    return [action1, action2, action3, action4]
```

## Get user preferences and time of day as context

Context can represent the current state of your application, system, environment, or user. The following methods create contextual features for the time of day, and taste preferences from user input.

```python
def get_user_timeofday():
    res={}
    time_features = ["morning", "afternoon", "evening", "night"]
    time = input("What time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n")
    try:
        ptime = int(time)
        if(ptime<=0 or ptime>len(time_features)):
            raise IndexError
        res['time_of_day'] = time_features[ptime-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", time_features[0] + ".")
        res['time_of_day'] = time_features[0]
    return res
```

```python
def get_user_preference():
    res = {}
    taste_features = ['salty','sweet']
    pref = input("What type of food would you prefer? Enter number 1.salty 2.sweet\n")
    
    try:
        ppref = int(pref)
        if(ppref<=0 or ppref>len(taste_features)):
            raise IndexError
        res['taste_preference'] = taste_features[ppref-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", taste_features[0]+ ".")
        res['taste_preference'] = taste_features[0]
    return res
```

## Create a learning loop

A Personalizer learning loop is a cycle of [Rank](#request-the-best-action) and [Reward](#send-a-reward) calls. In this quickstart, each rank call made to personalize the content is followed by a reward call to tell Personalizer how well the service performed.

The following code loops through a single cycle that asks the user their preferences at the command line, sends that information to Personalizer to select the best action, presents the selection to the customer to choose from among the list, then sends a reward to Personalizer signaling how well the service did in its selection.

```python
keep_going = True
while keep_going:

    eventid = str(uuid.uuid4())

    context = [get_user_preference(), get_user_timeofday()]
    actions = get_actions()

    rank_request = RankRequest( actions=actions, context_features=context, excluded_actions=['juice'], event_id=eventid)
    response = client.rank(rank_request=rank_request)
    
    print("Personalizer service ranked the actions with the probabilities listed below:")
    
    rankedList = response.ranking
    for ranked in rankedList:
        print(ranked.id, ':',ranked.probability)

    print("Personalizer thinks you would like to have", response.reward_action_id+".")
    answer = input("Is this correct?(y/n)\n")[0]

    reward_val = "0.0"
    if(answer.lower()=='y'):
        reward_val = "1.0"
    elif(answer.lower()=='n'):
        reward_val = "0.0"
    else:
        print("Entered choice is invalid. Service assumes that you didn't like the recommended food choice.")

    client.events.reward(event_id=eventid, value=reward_val)

    br = input("Press Q to exit, any other key to continue: ")
    if(br.lower()=='q'):
        keep_going = False
```

Add the following methods, which [get the content choices](#get-content-choices-represented-as-actions), before running the code file:

* `get_user_preference`
* `get_user_timeofday`
* `get_actions`

## Request the best action

To make a Rank request, you will need to provide: a list of 'RankActions' (_actions_), a list of context features (_context_), an optional list of actions (_excludeActions_) to remove from consideration by Personalizer, and a unique event ID to receive the response.

This quickstart has simple context features of time of day and user food preference. In production systems, determining and [evaluating](../concept-feature-evaluation.md) [actions and features](../concepts-features.md) can be a non-trivial matter.

```python
rank_request = RankRequest( actions=actions, context_features=context, excluded_actions=['juice'], event_id=eventid)
response = client.rank(rank_request=rank_request)
```

## Send a reward

To get the reward score to send in the Reward request, the program gets the user's selection from the command line, assigns a numeric value to the selection, then sends the unique event ID and the reward score as the numeric value to the Reward API.

This quickstart assigns a simple number as a reward score, either a zero or a 1. In production systems, determining when and what to send to the [Reward](../concept-rewards.md) call can be a non-trivial matter, depending on your specific needs.

```python
reward_val = "0.0"
if(answer.lower()=='y'):
    reward_val = "1.0"
elif(answer.lower()=='n'):
    reward_val = "0.0"
else:
    print("Entered choice is invalid. Service assumes that you didn't like the recommended food choice.")

client.events.reward(event_id=eventid, value=reward_val)
```

## Run the program

Run the application with the Python from your application directory.

```console
python sample.py
```

![The quickstart program asks a couple of questions to gather user preferences, known as features, then provides the top action.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)
