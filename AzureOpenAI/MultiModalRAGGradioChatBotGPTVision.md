# A Step-by-Step Guide to Build a MultiModal RAG Chatbot with Gradio Powered by Azure Open AI GPT Turbo with Vision #

In the rapidly evolving landscape of artificial intelligence, chatbots have established themselves as a crucial component in enhancing user interaction and improving customer service. As the technology continues to advance, so does the sophistication of these chatbots, with the latest development being the Multi Modal RAG (Retrieval-Augmented Generation) Chatbot. This advanced model not only responds to user text queries but also incorporates relevant image queries, taking the chatbot experience to a whole new level.

In this article, we'll delve into the creation of a cutting-edge Multi Modal RAG Chatbot, powered by Azure Open AI GPT Turbo with Vision. This unique configuration allows us to respond to user's queries based on images, enhancing the quality of its interactions. Furthermore, we'll explore how we utilized Gradio, a flexible UI library, to build an intuitive and user-friendly interface for our chatbot. Whether you're an AI enthusiast, a seasoned developer, or simply curious about the latest developments in chatbot technology, this article will provide valuable insights into the future of user interaction. So, let's dive in!

## High-level Architecture

<img width="900" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/6e2b3f43-f37f-41c9-b402-ef8d54081338">

## Multimodal Gradio RAG Chatbot Screengrabs

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/6069c447-4aa1-495a-8ba1-e9496cbe6894">

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f9f8fbf9-0d41-47a2-9ceb-90c6ac54efdd">

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d8dd9227-4e08-408a-a6f7-6d1b8de29874">

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/2baeb5e1-55dc-4bcd-b5e9-6cfe506e491d">

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/92012277-ddd9-4178-95d6-2da82bbf2036">

<img width="500" src="https://github.com/mahes-a/2023/assets/120069348/70732cb3-87e9-4ef4-ad27-3358590b9aa6">


## Prerequisites

Azure subscription with
  - Storage Account
  - Azure OpenAI's GPT-4 Turbo with vision capabilities , Refer [here](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/gpt-with-vision)
  - Azure AI Vision
  - Azure AI Search
  - Optional Azure Machine Learning Compute , Alternatively can Use Local Machine VS Code
  - Azure App Service to host the Gradio App

Gradio Multi Modal Chatbot , Refer [here](https://www.gradio.app/main/guides/multimodal-chatbot-part1)
  
## Technical Flow  
  
##### Back-end Flow: Indexing Images and Creating a Knowledge Base  
  
1. ##### Storage Account  
   - Source images and metadata are securely stored in an Azure Storage Account.  
  
2.  ##### Image Retrieval and Processing:  
   - A Machine Learning Notebook accesses and processes images and metadata from the Storage Account.  
  
3.  ##### Azure AI Vision:  
   - The service generates multi-modal image embeddings via API, capturing image features in a machine-readable vector format.  
  
4.  ##### Index Creation for AI Search:  
   - An index of image embeddings is created for efficient searching and is integrated into Azure AI Search.  
  
##### Front-End Flow: Augmenting and Generating Responses  
  
##### R. Data Retrieval:  
   - AI Search retrieves relevant images and text from the knowledge base in response to user queries.  
  
##### A. Augmentation:  
   - Retrieved data is augmented with prompts to enrich the context for response generation.  
  
##### G. Response Generation:  
   - Azure OpenAI's GPT-4 Turbo with vision capabilities processes the augmented data and generates relevant textual responses.  
  
##### User Interaction  
  
##### Gradio MultiModal Chatbot:  
   - A user-friendly interface for query submission and response reception, hosted on Azure App Service.  
  
##### Azure App Service:  
   - Ensures the chatbot's availability and scalability as a web application.  
  
##### Users:  
  - Users interact with the system via the Gradio interface, receiving intelligent responses informed by their queries. 

## Steps

#### Indexing Images and Creating a Knowledge Base from your own images

GPT-4 Turbo with Vision on your data allows the model to generate more customized and targeted answers using Retrieval Augmented Generation based on your own images and image metadata.

To provide your own image data for GPT-4 Turbo with Vision, Azure OpenAI’s vision model.

- Upload the images to Storage Account
  
- Prepare a Metadata Json file as below with the image url and image description

  <img width="613" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/9990546d-dd0a-4d12-ad58-a108ed324749">

- Clone the code  https://github.com/mahes-a/ImageIndexing
  
- Update the Metadata Json file with Image url and description  https://github.com/mahes-a/ImageIndexing/blob/main/productinfoMetdadata.json

- Create the .env file from the .env-sample and fill up the keys and endpoints
  
- Run the IndexImagestoCogSearch.ipynb either using a Azure Machine Learning Compute or through your local Visual Studio Code for development
  
- Once the code is run your own data knowledge base is created for querying

#### Creating  Multimodal Gradio Chatbot UI Powered by GPT-4 Turbo With Vision answering the User Image queries 

- Clone the code from https://github.com/mahes-a/MultiModalGradioChatBot to create the RAG UI
  
- Create the .env file from the .env-sample and fill up the keys and endpoints and the Azure Index Name created in the previous step

- Execute the MultiModalGradioChat.py to create the Gradio UI app , Open the local url of Gradio and test by uploading images and text questions

  <img width="320" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/b418155e-b5c2-42b8-8dc9-808fbd28c535">

- To Deploy the Gradio App to a Azure App Service refer [here](https://datasciencedojo.com/blog/web_app_for_gradio/)

#### Inspiration and References 
- Azure Python SDK https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/storage/azure-storage-blob
- https://github.com/MSUSAzureAccelerators/Azure-Cognitive-Search-Azure-OpenAI-Accelerator/blob/main/04-Complex-Docs.ipynb
- https://github.com/Azure/azureml-examples
- https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/use-your-image-data
- https://www.gradio.app/guides/multimodal-chatbot-part1
- https://www.analyticsvidhya.com/blog/2023/12/building-a-multimodal-chatbot-with-gemini-and-gradio/
  
