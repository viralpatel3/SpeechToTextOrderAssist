# Speech To Text Order Assist [Proposal]
## Table of Contents 

- [Summary](#Summary)
- [Dataset](#Dataset)
- [Proposed plan of research](#Proposed-plan-of-research)
- [Preliminary Analysis](#Preliminary-Analysis)
- [References](#References)

## Summary

In recent years, we have witnessed a revolution in the ability of computers to understand and to convert natural speech, especially with the application of deep neural networks (e.g., Google Home Mini, Alexa etc). In particular, automated speech systems are still struggling to recognize simple words and commands. They don’t engage in a conversation flow and force the user to adjust to the system instead of the system adjusting to the user.<br />

The key focus of this project is to speed up the process flow of ordering at restaurants. This is especially useful in a drive thru and take-out service to get through the queue quickly. Currently, a lot of resources and man-power are wasted by a person taking manual orders. Eventually, this could replace the person taking orders and save the money and time for both user and business end.<br />

The Quick Service Restaurant (QSR) Assist aims to accomplish the above. It would start out by hearing the order, transcribe it to text, map it to the items in the menu and finally create an invoice. From the converted text we would be able to classify the intent of that part of the speech such as adding, deleting, modifying and canceling an order. The extracted information is then used for keyword extraction and invoice generation.<br />
![Figure 1](/images/intro.png)
## Dataset:

We would be using the google dataset Taskmaster-1. This dataset consists of 13,215 taskbased dialogues in English. We will be using the conversation from only 2 of the 6 domains (ordering pizza and ordering coffee drinks) which are relevant to Quick Service Restaurants.<br />

Dialog annotations are based on the API calls associated with each type of task-based dialog. The full JSON description of the ontology can be found in ontology.json. Each conversation was annotated by two workers. Both annotations are included in this collection.<br />

The ontology.json can act as a reference to validate the invoice generated in the end. However, this doesnot validate intent classification. We are looking at various APIs that would help us test the intent classifications in further steps.<br />

## Proposed plan of research:
The Quick Service Restaurant (QSR) Assist will have the following key functionalities:
### Result 1
![Flow-diagram](/images/Flow-diagram.png)
* Speech-to-Text : Listen for user text and transcribe for speech to text.

```json
Input speech: ”Can I please get a Veggie Pizza and a coke bottle.”
Transcribed to: ”Can I please get a Veggie Pizza and a coke bottle.”
```
This part of the QSR Assist will be achieved by Google Speech to text API.

* Entity-Intent Recognition: Uses the text transcribed to recognize the entity(object) and intent(action). <br /> 
eg: ”Please add olives to the pizza”.

 ```json
Entities: Pizza.
Sub-Entities: Olives.
Intent: Customize
```

The object entity recognition is done to identify and classify items. This can be done by tokenizing and classifying the words. We used Wit.ai API this would identify items as well as intent from the statements this information extraction step will help us with information extraction. <br />

The next steps will be to validate the intent classified by the API as `ontology.json` would help us validate the final order but not the intent which is an intermediate step. Also identify the order quantity correctly which is not achieved by using Wit.ai API.

* Invoice Generation: The entity-intent is queried in the database to create order and invoice. This can be achieved by querying on our data and creating consolidated data frames. At this point, the order is mapped to the items in the menu by the unique identification code which would fetch prices and generate invoices in the end. We would create an SQLlite database for development purpose with item and their prices. It would be simply updated by adding, modifying or deleting item and item prices. <br />
Output: 
INVOICE

 Items | Quantity | price
  -----|----------|----- 
 Pizza | 1 | $10  
 olives (Pizza) | 1 | $1.5 
 Coke | 4 | $12  
 Sub | 1 | $9   
 Pizza(5 cheese pizza) | 1 | $12  
 Burger | 1 | $5   
 Fries | 1 | $2.5 
 Total | 10| $53  

* Evaluation: 

We will be evaluating the results from `wit.ai` and from our hybrid model to have a structure that would have item and quantity to describe the order. It can be matched against the values from `ontology.json`. We would use binary scores if the order was correct it would be a correct classification otherwise it would be an incorrect classification. We will be using F1-scores to quantify our results.<br />
We will also be validating our intents using API clients to validate the intermediate steps. Which we cannot at the current point. As this can improve our final results and also give understanding about why we get the incorrect results.<br />

## Preliminary Analysis:
The GOOGLE NLP API was tested to get the intent and objects from some text orders. Here are those examples:
* ”One Burger and Two Fries”
```json
{
  "entities": [
    {
      "mentions": [
        {
          "text": {
            "beginOffset": 12,
            "content": "burgers"
          },
          "type": "COMMON"
        }
      ],
      "metadata": {},
      "name": "burgers",
      "salience": 0.58150464,
      "type": "OTHER"
    },
    {
      "mentions": [
        {
          "text": {
            "beginOffset": 24,
            "content": "fries"
          },
          "type": "COMMON"
        }
      ],
      "metadata": {},
      "name": "fries",
      "salience": 0.4184954,
      "type": "CONSUMER_GOOD"
    },
    {
      "mentions": [
        {
          "text": {
            "beginOffset": 7,
            "content": "five"
          },
          "type": "TYPE_UNKNOWN"
        }
      ],
      "metadata": {
        "value": "5"
      },
      "name": "five",
      "salience": 0.0,
      "type": "NUMBER"
    }
  ],
  "language": "en"
}
```
* "I want to order a burger, with coke and fries at the side"
```json
  "entities": [
    {
      "mentions": [
        {
          "text": {
            "beginOffset": 18,
            "content": "burger"
          },
          "type": "COMMON"
        }
      ],
      "metadata": {},
      "name": "burger",
      "salience": 0.36076498,
      "type": "OTHER"
    },
    {
      "mentions": [
        {
          "text": {
            "beginOffset": 31,
            "content": "coke"
          },
          "type": "COMMON"
        }
      ],
      "metadata": {},
      "name": "coke",
      "salience": 0.33710873,
      "type": "OTHER"
    },
    {
      "mentions": [
        {
          "text": {
            "beginOffset": 53,
            "content": "side"
          },
          "type": "COMMON"
        }
      ],
      "metadata": {},
      "name": "side",
      "salience": 0.18460932,
      "type": "OTHER"
    },
    {
      "mentions": [
        {
          "text": {
            "beginOffset": 40,
            "content": "fries"
          },
          "type": "COMMON"
        }
      ],
      "metadata": {},
      "name": "fries",
      "salience": 0.11751698,
      "type": "CONSUMER_GOOD"
    }
  ],
  "language": "en"
}
```

## References
* <a href = "https://cloud.google.com/natural-language/" >`Natural Language - Google Cloud`</a>
* <a href = "https://ai.google/tools/datasets/taskmaster-1" >`Dataset: taskmaster-1`</a>
* <a href = "https://cloud.google.com/text-to-speech/" >`Text to Speech - Google Cloud`</a>
* <a href = "https://www.topbots.com/ai-nlp-research-papers-acl2019/" >`TOP NLP RESEARCH PAPERS WITH BUSINESS APPLICATIONS FROM ACL 2019`</a>
