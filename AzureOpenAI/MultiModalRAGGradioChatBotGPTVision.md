# A Step-by-Step Guide to Build a MultiModal RAG Chatbot with Gradio Powered by Azure Open AI GPT Turbo with Vision #

In the rapidly evolving landscape of artificial intelligence, chatbots have established themselves as a crucial component in enhancing user interaction and improving customer service. As the technology continues to advance, so does the sophistication of these chatbots, with the latest development being the Multi Modal RAG (Retrieval-Augmented Generation) Chatbot. This advanced model not only responds to user text queries but also incorporates relevant image queries, taking the chatbot experience to a whole new level.

In this article, we'll delve into the creation of a cutting-edge Multi Modal RAG Chatbot, powered by Azure Open AI GPT Turbo with Vision. This unique configuration allows us to respond to user's queries based on images, enhancing the quality of its interactions. Furthermore, we'll explore how we utilized Gradio, a flexible UI library, to build an intuitive and user-friendly interface for our chatbot. Whether you're an AI enthusiast, a seasoned developer, or simply curious about the latest developments in chatbot technology, this article will provide valuable insights into the future of user interaction. So, let's dive in!

## High-level Architecture

<img width="760" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/6e2b3f43-f37f-41c9-b402-ef8d54081338">

## Multimodal Gradio RAG Chatbot Screengrabs

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/6069c447-4aa1-495a-8ba1-e9496cbe6894">

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f9f8fbf9-0d41-47a2-9ceb-90c6ac54efdd">

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d8dd9227-4e08-408a-a6f7-6d1b8de29874">

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/2baeb5e1-55dc-4bcd-b5e9-6cfe506e491d">

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/92012277-ddd9-4178-95d6-2da82bbf2036">

<img width="500" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8d605449-386d-4c25-8b00-469ca9c8f5f2">



## Prerequisites

Azure subscription with
  - Storage Account
  - Azure OpenAI's GPT-4 Turbo with vision capabilities , Refer [here](https://learn.microsoft.com/en-us/azure/ai-services/openai/how-to/gpt-with-vision)
  - Azure AI Vision
  - Azure AI Search
  - Azure Machine Learning Compute/ Optional for Developmnet can use local VS Code
  - Azure App Service to host the Gradio App

Gradio Multi Modal Chatbot , Refer [here](https://www.gradio.app/main/guides/multimodal-chatbot-part1)
  
## Technical Flow  
  
#### Back-end Flow: Indexing Images and Creating a Knowledge Base  
  
1. **Storage Account**:  
   - Source images and metadata are securely stored in an Azure Storage Account.  
  
2. **Image Retrieval and Processing**:  
   - A Machine Learning Notebook accesses and processes images and metadata from the Storage Account.  
  
3. **Azure AI Vision**:  
   - The service generates multi-modal image embeddings via API, capturing image features in a machine-readable vector format.  
  
4. **Index Creation for AI Search**:  
   - An index of image embeddings is created for efficient searching and is integrated into Azure AI Search.  
  
#### Front-End Flow: Augmenting and Generating Responses  
  
R. **Data Retrieval**:  
   - AI Search retrieves relevant images and text from the knowledge base in response to user queries.  
  
A. **Augmentation**:  
   - Retrieved data is augmented with prompts to enrich the context for response generation.  
  
G. **Response Generation**:  
   - Azure OpenAI's GPT-4 Turbo with vision capabilities processes the augmented data and generates relevant textual responses.  
  
#### User Interaction  
  
- **Gradio MultiModal Chatbot**:  
   - A user-friendly interface for query submission and response reception, hosted on Azure App Service.  
  
- **Azure App Service**:  
   - Ensures the chatbot's availability and scalability as a web application.  
  
- **Users**:  
  - Users interact with the system via the Gradio interface, receiving intelligent responses informed by their queries. 

## Steps
