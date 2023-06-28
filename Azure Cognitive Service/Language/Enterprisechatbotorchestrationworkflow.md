# Azure Cognitive Service for Language : Build Multifunctional Chat Bot powered by Orchestration Workflow integrating Custom question answering and Conversational Language Understanding 

## High-level Architecture

![image](https://github.com/mahes-a/2023/assets/120069348/b9d169e4-49e0-45a3-8720-7a2d089629a8)

## High-level Bot Conversation Flow

![image](https://github.com/mahes-a/2023/assets/120069348/c470b7ac-c29a-4598-ae3e-dedcd1855ee4)

## Sample Bot repsonses via Teams Channel

![image](https://github.com/mahes-a/2023/assets/120069348/b495f7b7-37e7-4601-a7c8-712f99d66e5f)


## Flow and Getting Started

- In this sample , our conversational bot retreives relevant info by routing incoming requests to the correct service by utilizing Natural Language Processing (NLP) & Natural Language Understanding (NLU) and machine-learning intelligence.
- Sample bot based on user Input will either provide appropriate answers from customer provided corpus or classify as flight booking intent and extract entities for flight information or classify as weather intent and extract entities for retreive weather info.
- Based on the intent and entities extracted respective API's are executed and info is returned back to user

## Required Components

- **Azure Bot Service**- Microsoft Bot Framework and Azure Bot Service are a collection of libraries, tools, and services that let you build, test, deploy, and manage intelligent bots. The Bot Framework includes a modular and extensible SDK for building bots and connecting to AI services. With this framework, developers can create bots that use speech, understand natural language, answer questions, and more. Learn more [here](https://learn.microsoft.com/en-us/azure/bot-service/bot-service-overview?view=azure-bot-service-4.0)
  
- **Azure Cognitive Service for Language Custom Question answering** provides cloud-based Natural Language Processing (NLP) that allows you to create a natural conversational layer over your data. It is used to find appropriate answers from customer input or from a project. Learn more [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/question-answering/overview)
  
- **Azure Cognitive Service for Language Conversational language understanding (CLU)** enables users to build custom natural language understanding models to predict the overall intention of an incoming utterance and extract important information from it. CLU only provides the intelligence to understand the input text for the client application and doesn't perform any actions. Learn more [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/conversational-language-understanding/overview)

- **Azure Cognitive Service for Language Orchestration workflow** is a cloud-based API service that applies machine-learning intelligence to enable you to build orchestration models to connect Conversational Language Understanding (CLU), Question Answering projects and LUIS applications. Learn more [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/orchestration-workflow/overview)

- **Flight booking and Weather API** from Rapid API
  

## Prerequisites

- Bot Composer , can be installed from [here](https://learn.microsoft.com/en-us/composer/install-composer?tabs=windows). 
- Language Studio , access from [here](https://language.cognitive.azure.com/home)
- Sign up for free Weather and Flight API from Rapid API
  
  
    https://rapidapi.com/worldapi/api/open-weather13
  
    https://rapidapi.com/3b-data-3b-data-default/api/skyscanner44
  
- **Optional** - Azure subscription with access enabled for the Azure OpenAI service as an optional resource. You can request access [here](https://aka.ms/oaiapply).

  
## Things to Note

- The sample is created using Empty bot template rather than using language templates for simplicity
  
- The idea behind the sample is the understand the user intent and retrieve relevant info , this is showcased using Public API calls but this very well can be internal
  systems API calls
  
- The same intent classification and Question answering can be done via Azure Open AI
  
## Steps 

**Lets Create a Q&A resource first followed by CLU resource and then use orchestrtion workflow to integrate both projects**

### Create Custom question answering

- Go to Language Studio https://language.cognitive.azure.com/home and sign in to your subscription and choose Custom Answering

  <img width="1073" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/e015a203-5837-4f0f-bf78-bd6c1399c262">

- Click Create new project

     <img width="523" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/26bca51c-1c77-4401-94ac-a43172519c5c">

- Type Project Name , description and Default answer and click Review and Finish and create the project
   
     <img width="658" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/11503629-7b36-4a82-bbb4-f5d0df14f4d0">

- Add sources (corpus) to answer queries from
      <img width="550" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/213af89f-97c6-4a00-8070-d81d7a98e258">

- For example , you can add Azure Pricing site
  
   <img width="1126" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a1c93bf4-2404-4363-97d5-4a4269ac09ce">

- Click on the "Edit Knowledge base" section to validate   the Question and Answer pairs created

    <img width="953" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/6bb966ee-b1af-47c3-bbc0-2947fc3f3418">

    
 - You can add our own question and answer

    <img width="950" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/7894a9a1-a95d-46d2-acef-7b101fb12689">
    
- Click on test icon to test the feature , after testing save the project
  
      <img width="308" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d46abc60-147f-49d2-917e-4494fe034aed">

- Once tested move to the deploy tab and deploy the knowledge base , If we plan to use only Q&A feature in the bot , you could create a bot right from this screen with no code and deploy to your subscription
  
  <img width="502" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/3e78694d-d7d2-403f-bb6f-4721b0d8d9ab">
  
### Create Conversational Language Understanding

- Go to Language Studio https://language.cognitive.azure.com/home and sign in to your subscription and choose Conversational Language Understanding

  <img width="1073" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/e015a203-5837-4f0f-bf78-bd6c1399c262">

  
- Create a new project, provide information and create the project
  
  <img width="617" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d149b4d3-be48-4e05-a66f-02b768644f2c">

- For building the sample lets use the Flight and weather sample by importing the FlightBooking.json from [here](https://github.com/Azure-Samples/cognitive-service-language-samples/blob/main/CoreBotWithCLU/CognitiveModels/FlightBooking.json)
  
  <img width="491" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/4b7bd514-3d4f-4a56-b9a2-0a7e20155e90">

  
- The Json file incluses Book Flight, Get Weather, and None intents and flightdate , fromcity , tocity entities
  
  <img width="622" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/cd5ce9a4-c4d9-44ea-aa8c-a0ae5c40d5d7">
  
-  Refer [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/conversational-language-understanding/how-to/build-schema) to learn more about intents and enitities
  
-  f





  
    
