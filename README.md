## Question Answering System using DistilRoBERTa Embeddings and Dialogflow

Dialogflow is a natural language understanding platform that makes it easy to design and build a conversational user interface. For question and answering system, it offers a knowledge connector to the knowledge base so that it can generate the responses based on the list of question-answer pairs. At the point of writing, this connector is still a beta feature.

## How It Works
Each time a user enters a query into the chatbot, the Dialogflow will send the query to the local server via webhook and ngrok. The model in the local server selects the closest matching response from the knowledge base and then returns the most likely response to the query. This response is then sent back to the user in Dialogflow. In this instance, i have illustrated it with a HR domain example. But any domain works as well.

![Overview](https://user-images.githubusercontent.com/85472923/121799695-166b7580-cc60-11eb-92dc-770bac62cc84.jpg)

## Step 1: Set up in Dialogflow 
1) Sign up for a Dialogflow account on https://cloud.google.com/dialogflow/docs/
2) Create a project and an agent in Dialogflow based on the steps on https://developers.google.com/assistant/conversational/df-asdk/dialogflow/project-agent. 
If you have multiple intents in the agent, you may add training phrases to this HR_FAQ intent. However, there is no need to add the responses as the responses will be generated from our model in the local server.
 
## Step 2: Develop Model in Python using Flask 
Pre-trained DistilRoBERTa model is used to extract the sentence embeddings due to the small number of question-answer pairs in the knowledge base. Other variations of pre-trained model will also work, as long as the response time is within reasonable time limit. RoBERTa model was considered but not selected as the final model as it took about 16 seconds to generate a response as compared to 2 seconds by DistilRoBERTa.

Based on the sentence embeddings, cosine similarity is calculated between the query and questions to identify the most similar question. The model then uses the retrieval approach to search for the corresponding answer that matches the most similar question. 

![Model](https://user-images.githubusercontent.com/85472923/121800851-b75d2f00-cc66-11eb-9fbd-c93931475941.jpg)

## Step 3: Deploy Model & Integrate with Dialogflow using Webhook & Ngrok
1) Download ngrok on your computer at https://ngrok.com/download. It is a popular tunneling tool to expose the application to the internet and receive the webhook from Dialogflow to the application. 
2) Start ngrok. 
3) We assume that our application is running locally on port 5000 for flask. Hence, type in "ngrok http 5000" into the ngrok application.  
4) Copy the https forwarding address to integrate with Dialogflow.
5) Paste the https url to the Webhook field in the Fulfillment tab of Dialogflow. Add "/webhook" to the end of the url. The format of the final url will look like https://xxxxxxxxxxxx.io/webhook. For more information, you may refer to the "Enable and manage fulfillment" section on https://cloud.google.com/dialogflow/es/docs/fulfillment-webhook
6) To enable fulfillment, go to the HR_FAQ intent and turn on the "enable webhook call for this intent". Click save.


## Step 4: Test the Chatbot
Enter a test query in the test console of Dialogflow to make sure that the chatbot is working well.
