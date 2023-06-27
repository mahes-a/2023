# Azure Cognitive Service for Language : Build Multifunctional Chat Bot powered by Orchestration Workflow integrating Custom question answering and Conversational Language Understanding 

## High-level Architecture

![image](https://github.com/mahes-a/2023/assets/120069348/b9d169e4-49e0-45a3-8720-7a2d089629a8)

## High-level Bot Conversation Flow

![image](https://github.com/mahes-a/2023/assets/120069348/c470b7ac-c29a-4598-ae3e-dedcd1855ee4)


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


  
  

  
  
    
