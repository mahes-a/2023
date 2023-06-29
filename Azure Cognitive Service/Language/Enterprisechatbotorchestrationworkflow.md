# Azure Cognitive Service for Language: Build Multifunctional Chat Bot powered by Orchestration Workflow integrating Custom question answering and Conversational Language Understanding 

## High-level Architecture

![image](https://github.com/mahes-a/2023/assets/120069348/b9d169e4-49e0-45a3-8720-7a2d089629a8)

## High-level Bot Conversation Flow

![image](https://github.com/mahes-a/2023/assets/120069348/c470b7ac-c29a-4598-ae3e-dedcd1855ee4)

## Sample Bot responses via Teams Channel

![image](https://github.com/mahes-a/2023/assets/120069348/b495f7b7-37e7-4601-a7c8-712f99d66e5f)


## Flow and Getting Started

- In this sample, our conversational bot retrieves relevant info by routing incoming requests to the correct service by utilizing Natural Language Processing (NLP) & Natural Language Understanding (NLU) and machine-learning intelligence.
- Sample bot based on user Input will either provide appropriate answers from customer provided corpus or classify as flight booking intent and extract entities for flight information or classify as weather intent and extract entities to retrieve weather info.
- Based on the intent and entities extracted respective APIs are executed and info is returned back to user

## Required Components

- **Azure Bot Service**- Microsoft Bot Framework and Azure Bot Service are a collection of libraries, tools, and services that let you build, test, deploy, and manage intelligent bots. The Bot Framework includes a modular and extensible SDK for building bots and connecting to AI services. With this framework, developers can create bots that use speech, understand natural language, answer questions, and more. Learn more [here](https://learn.microsoft.com/en-us/azure/bot-service/bot-service-overview?view=azure-bot-service-4.0)
  
- **Azure Cognitive Service for Language Custom Question answering** provides cloud-based Natural Language Processing (NLP) that allows you to create a natural conversational layer over your data. It is used to find appropriate answers from customer input or from a project. Learn more [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/question-answering/overview)
  
- **Azure Cognitive Service for Language Conversational language understanding (CLU)** enables users to build custom natural language understanding models to predict the overall intention of an incoming utterance and extract important information from it. CLU only provides the intelligence to understand the input text for the client application and doesn't perform any actions. Learn more [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/conversational-language-understanding/overview)

- **Azure Cognitive Service for Language Orchestration workflow** is a cloud-based API service that applies machine-learning intelligence to enable you to build orchestration models to connect Conversational Language Understanding (CLU), Question Answering projects and LUIS applications. Learn more [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/orchestration-workflow/overview)

- **Flight booking and Weather API** from Rapid API
  

## Prerequisites

- Bot Composer, can be installed from [here](https://learn.microsoft.com/en-us/composer/install-composer?tabs=windows). 
- Language Studio, access from [here](https://language.cognitive.azure.com/home)
- Sign up for free Weather and Flight API from Rapid API
  
  
   Weather API [here](https://rapidapi.com/worldapi/api/open-weather13/)
  
    Flight API [here](https://rapidapi.com/3b-data-3b-data-default/api/skyscanner44)
  
- **Optional** - Azure subscription with access enabled for the Azure OpenAI service as an optional resource. You can request access [here](https://aka.ms/oaiapply).

  
## Things to Note

- The sample is created using General bot template for simplicity
  
- The idea behind the sample is the understand the user intent and retrieve relevant info, this is showcased using Public API calls but this very well can be internal
  systems API calls
  
- Azure Open AI Can handle intent classificationa nd entirty extraction and Q&A over your own data (RAG pattern) 

- UI can be PVA bot 

  
## Steps 

**Let’s Create a Custom Q&A resource first followed by CLU resource and then use orchestration workflow to integrate both projects**

### Create Custom question answering

- Go to Language Studio https://language.cognitive.azure.com/home and sign into your subscription and choose Custom Answering

  <img width="1073" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/e015a203-5837-4f0f-bf78-bd6c1399c262">

- Click Create new project

     <img width="523" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/26bca51c-1c77-4401-94ac-a43172519c5c">

- Type Project Name, description and Default answer and click Review and Finish and create the project
   
     <img width="658" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/11503629-7b36-4a82-bbb4-f5d0df14f4d0">

- Add sources (corpus) to answer queries from
      <img width="550" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/213af89f-97c6-4a00-8070-d81d7a98e258">

- For example, you can add Azure Pricing site
  
   <img width="1126" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a1c93bf4-2404-4363-97d5-4a4269ac09ce">

- Click on the "Edit Knowledge base" section to validate   the Question and Answer pairs created

    <img width="953" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/6bb966ee-b1af-47c3-bbc0-2947fc3f3418">

    
 - You can add our own question and answer

    <img width="950" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/7894a9a1-a95d-46d2-acef-7b101fb12689">
    
- Click on test icon to test the feature, after testing save the project
  
  <img width="308" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d46abc60-147f-49d2-917e-4494fe034aed">

- Once tested move to the deploy tab and deploy the knowledge base, If we plan to use only Q&A feature in the bot, you could create a bot right from this screen with no code and deploy to your subscription
  
  <img width="502" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/3e78694d-d7d2-403f-bb6f-4721b0d8d9ab">
  
### Create Conversational Language Understanding

-Refer [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/conversational-language-understanding/overview#project-development-lifecycle) to undesrtand the CLU devlopment lifecycle 

- Go to Language Studio https://language.cognitive.azure.com/home and sign in to your subscription and choose Conversational Language Understanding

  <img width="1073" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/e015a203-5837-4f0f-bf78-bd6c1399c262">

  
- Create a new project, provide information and create the project
  
  <img width="617" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d149b4d3-be48-4e05-a66f-02b768644f2c">

- For building the sample let’s use the Flight and weather sample by importing the FlightBooking.json from [here](https://github.com/Azure-Samples/cognitive-service-language-samples/blob/main/CoreBotWithCLU/CognitiveModels/FlightBooking.json)
  
  <img width="491" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/4b7bd514-3d4f-4a56-b9a2-0a7e20155e90">

  
- The Json file includes Book Flight, Get Weather, and None intents and flightdate , fromcity , tocity entities
  
  <img width="622" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/cd5ce9a4-c4d9-44ea-aa8c-a0ae5c40d5d7">
  
-  Refer [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/conversational-language-understanding/how-to/build-schema) to learn more about intents and entities
  
-  Go the Data labeling tab to understand the label utterances and entities extracted

   <img width="1133" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a45c1e66-7989-48cc-97fe-bc58d89c4ec7">

   <img width="523" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/735e1a45-7769-4654-8192-26ca4cac40cf">


-  If you have Azure Open AI resource available, click on "Suggest utterances" to get the sample utterances from Open AI , review and save them

  <img width="242" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a3fb6332-118c-4155-95fa-0ed1fb2e883c">

- Move to Training jobs section , Training is the process where the model learns from your labeled utterances. Start a training job by providing the model name.

   <img width="1127" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d1109485-4b9d-490f-a331-e6ef1ca37f33">

    <img width="757" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/95a42294-73aa-4dfc-8ab2-95082fb13485">

-  Click on the Model Performance to understand our model performance , Refer [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/conversational-language-understanding/concepts/evaluation-metrics) to understand evaluation metrics
  
 <img width="1073" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/0398d554-5c0e-4b3c-af3e-52c192f513d4">


- Add more labels and utterances to better train the model, in this sample we are not adding more labels 

- Move to the Deployment section to deploy a model based on the training

  <img width="922" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/57aaf3e1-adae-4f4e-886a-7b004140f522">


- Move to Testing tab to test the model with sample questions
  
  <img width="586" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a55aeca7-1558-4bea-bd9d-c151736784e5">

- Now the Model predicts the Top intent by confidence score and the associated entities extracted 

### Create Orchestration workflow

- Lets move to the home page of the Language studio and open Orchestration workflow project to orchestrate both CLU & Q&A resources we created in above steps 

  <img width="956" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a4edfdf0-cc5b-42b7-8a31-99c9f39d8a0e">

- Create new Orchestration project

  <img width="715" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/0b1c9e30-dbb6-43ed-8780-cf0b2d1d7cc0">


- After creating the project add intent and add the CLU and Q&A project created in above steps
  
  
    <img width="579" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/17fe5e46-b987-4365-b705-1fe91548b027">
    

     <img width="578" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ff815c6b-4a92-47e4-89cd-0439ef2f9ac6">

- Since, we have already labeled our CLU , move on to training and create a training job

    <img width="1139" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/97dd3492-3b2b-4ac7-b033-f578241fd175">
    
- Go to Model performance to evaluate the model performance after training
    <img width="923" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/4889d67b-6a0d-42c1-90a6-0594004099b3">

- Deploy the trained model and note down the prediction url and the OCP- APIM Subscription Key for usage in the bot
    <img width="462" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/402789f2-bbc0-4d21-9c83-a3f24464a118">

- You can test the model in the Testing deployments tab

  ### Create the bot and connect to Orchestration workflow endpoint 

- Go to bot composer and create a empty bot. As we are using the orchestration workflow that integrates both CLU & Q&A functionalities, We are going to make a HTTP request and determine the intent . This sample does not use Language/Q&A bot templates. 
  
  <img width="739" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/7d876a70-69f5-41e4-a36d-b63c8c580bb6">

- Open the configuration section and toggle on Advanced Settings and add the settings listed in image  the keys and the endpoints

<img width="1196" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/7a62be78-b126-40cb-84ec-5341464d8822">


 

- The Cognitive_service_endpoint and cognitive_API_key can be retrieved from the orchestration prediction url and the OCP- APIM Subscription Key

  <img width="254" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/0e3d3f33-30d0-4c7c-97c1-0f5c00b5ea31">

  
- When you register with the Rapid API , you will get the API_key , the Weather_endpoint and flights_API endpoint are from Rapid API
  
- Open your bot "Unknown Intent" flow, for simplicity/brevity , we are not defining intents in our bot matching to CLU intents or creating seperate dialog for each intent
  
- Make a HTTP request to the orchestration prediction url using the Ocp-APIm-Subscription-Key

  <img width="348" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d7b6f66e-d1d4-499e-927d-6eb118b594c8">
  
  <img width="475" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/40b97a23-2a91-4126-8dd6-8ad5dff88cdc">

  
- The Orchestration workflow recognizes the intents and responds back, body of the http request should be below format when answered from the Custom Q&A resource
  ```{

  "“lgType”": "“Activity”",

  "“text”": {

    "statusCode": 200,

    "reasonPhrase": "OK",

    "headers": {

      .....

    },

    "content": {

      "kind": "ConversationResult",

      "result": {

        "query": "User question here",

        "prediction": {

          "topIntent": "PricingQA",

          "projectKind": "Orchestration",

          "intents": {

            "PricingQA": {

              "confidenceScore": 1,

              "targetProjectKind": "QuestionAnswering",

              "result": {

                "answers": [

                  {

                    "questions": [

                      "Question from User"

                    ],

                    "answer": "Answer from Q&A",

                    "confidenceScore": 1.0,

                    "id": 17,

                    "source": "source url",

                    "metadata": {},

                    "dialog": {

                      "isContextOnly": false,

                      "prompts": []

                    }

                  }

                ]

              }

            },

            "BookFlightWeather": {

              "confidenceScore": 0.2100786,

              "targetProjectKind": "Conversation"

            },

            "None": {

              "confidenceScore": 0,

              "targetProjectKind": "NonLinked"

            }

          }

        }

      }

    }
  } }

  
  
- The response body when it is determined as flight booking intent and the extracted entities are as below (truncated body for brevity)

    ``` "topIntent": "BookFlightWeather",

          "projectKind": "Orchestration",

          "intents": {

            "BookFlightWeather": {

              "confidenceScore": 0.9304501,

              "targetProjectKind": "Conversation",

              "result": {

                "query": "price for flight between City1 and City2 on **date**",

                "prediction": {

                  "topIntent": "BookFlight",

                  "projectKind": "Conversation",

                  "intents": [

                    {

                      "category": "BookFlight",

                      "confidenceScore": 0.7823027

                    },

                    {

                      "category": "GetWeather",

                      "confidenceScore": 0.49844158

                    },

                    {

                      "category": "None",

                      "confidenceScore": 0.39740455

                    },

                    {

                      "category": "Cancel",

                      "confidenceScore": 0.12159197

                    }

                  ],

                  "entities": [

                    {

                      "category": "fromCity",

                      "text": "City1",

                      "offset": 25,

                      "length": 2,

                      "confidenceScore": 1

                    },

                    {

                      "category": "toCity",

                      "text": "City2",

                      "offset": 32,

                      "length": 3,

                      "confidenceScore": 1

                    },

                    {

                      "category": "flightDate",

                      "text": "date from user input",

                      "offset": 39,

                      "length": 11,

                      "confidenceScore": 1,

- We add a branch switch condition based on the top intent from Orchestration workflow result Json , The condition to retrieve is , the sample intents are hard coded in the branch switch statement 
  
  =turn.cluAPIresponse.content.result.prediction.topIntent

  <img width="536" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/bc64b7f4-d160-4541-a616-ef05280cd165">

  <img width="260" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/527defb9-1364-467a-984e-c78c065d5a81">


- If the intent is from Q&A , we respond with the answer to user

  <img width="324" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/eeb16e67-0459-4d95-99a6-13bdc689432c">


- Our Clu has two related intents and entities from same project, Book flight and get weather. For best practices for intents refer [here](https://learn.microsoft.com/en-us/azure/cognitive-services/language-service/conversational-language-understanding/concepts/best-practices)
  
- We could define two dialogs for each intent and call the dialog from unknown intent, here for simplicity we keep the conversation in the same dialog
  
- Add a branch/switch using the "=turn.cluAPIresponse.content.result.prediction.intents.BookFlightWeather.result.prediction.topIntent" to direct the flow for weather and flight

  <img width="261" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/287bda67-57f1-4e54-ad55-7e9ec53eb592">

- Retrieve the related entities from the response Json , the entities are present within the below properties of Json
  =turn.cluAPIresponse.content.result.prediction.intents.BookFlightWeather.result.prediction.entities
  
**For the weather flow**

 <img width="362" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/c906431d-d67a-410e-879e-22b88df69961">

- Set the weather city to a property
  
  <img width="379" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8c1d6836-e9a2-462b-9c09-cad0cc149d00">
  
- Call the weather API by passing the city to retrieve weather information

  <img width="562" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d5e98c86-189b-447f-a435-9d2547bb796f">

- The url for weather would be https://open-weather13.p.rAPIdAPI.com/city/**city name** , ensure to pass the API key in the header property X-RAPIdAPI-Key

- Validate the result from API and send response

    <img width="331" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/cc42bf0e-48d1-4ffb-851e-38e402f21ace">

**For the Booking flight flow**

<img width="298" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ba7ada58-7ee4-48fd-ae7a-2d4ccf3f4e15">

- The flight URL is of below format
  https://skyscanner44.p.rAPIdAPI.com/search?adults=1&origin=**Fromcity**&destination=**tocity**&departureDate=**date**&currency=EUR&locale=en-GB&market=UK
  
- The CLU will respond with the extracted entities in the Json result 

     "entities": [

                    {

                      "category": "fromCity",

                      "text": "Source City",

                      "offset": 25,

                      "length": 2,

                      "confidenceScore": 1

                    },

                    {

                      "category": "toCity",

                      "text": "Destination City",

                      "offset": 32,

                      "length": 3,

                      "confidenceScore": 1

                    },

                    {

                      "category": "flightDate",

                      "text": "date",


- The entities extracted are passed along the query string of the API URL . The API takes in only IATA codes, City to IATA codes API can be used to retrieve the IATA code from user provided city.

  <img width="538" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ae3c5340-f4f7-4ba3-abea-a444338d5431">

- API to get IATA code https://skyscanner44.p.rAPIdAPI.com/autocomplete?query=**cityname**
<img width="682" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/b513598b-392e-4909-9441-23a892b5f702">

- Once Price is fetched from API , validate , format and respond back to user


<img width="545" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/c4b7948e-812c-4feb-8b45-75564ca68c97">


##  Publishing and Temas channel for the bot

-  Publish the Bot to Azure from bot composer [refer here](https://learn.microsoft.com/en-us/composer/how-to-publish-bot?tabs=v2x)

-  To Acess the bot in teams channel [refer here](https://techcommunity.microsoft.com/t5/modern-work-app-consult-blog/publish-bot-app-to-teams-channel-with-bot-framework-composer-and/ba-p/3341876)

