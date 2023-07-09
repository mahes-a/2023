# Unlock Data-Driven Insights with Power BI and Azure Open AI Integration

Using Power BI with OpenAI models can help you unlock the full potential of your data, gain deeper insights, and make data-driven decisions more effectively. This article will demonstrate the step-by-step process of integrating Power BI with Azure OpenAI. By following this guide, you will learn how to seamlessly combine the power of Power BI's data visualization capabilities with Azure OpenAI's advanced natural language processing, enabling you to unlock new insights and make data-driven decisions.


## High-level Architecture

![image](https://github.com/mahes-a/2023/assets/120069348/9f7b4c22-3480-495d-bc49-46f046fda51e)

## Prerequisites

- Azure subscription with access enabled for the Azure OpenAI service. You can request access [here](https://aka.ms/oaiapply). 
- Power BI
- Visual Studio / Visual Studio Code

## Getting Started

This article showcases the powerful combination of OpenAI and Power BI for two essential tasks: Customer feedback analysis and Knowledge extraction and summarization. Let's delve deeper into the benefits of integrating these technologies:

1.	Customer feedback analysis:
By leveraging OpenAI's natural language processing capabilities and Power BI's data visualization tools, businesses can gain valuable insights from customer feedback. This integration allows for sentiment analysis, identifying positive and negative sentiments expressed by customers. With Power BI's interactive dashboards and visualizations, businesses can track sentiment trends over time, identify patterns, and pinpoint areas requiring improvement.

2.	Information Retrieval:
The combination of information retrieval from OpenAI and visualizing it in Power BI offers businesses the advantage of efficiently extracting and presenting relevant information in a visually compelling way. By leveraging OpenAI's advanced natural language processing capabilities and Power BI's data visualization tools, businesses can quickly retrieve and analyze data, gain valuable insights from research papers, news articles, or other textual sources and make informed decisions

## Technical Flow 

- In this sample, we build an Azure Function App which receives prompts as Inputs, executes the Azure Open AI API and responds with data results from Azure Open AI in a JSON format
- Power Query (within Power BI) will make Http Post calls to the function App with the prompts and transforms the results from Azure Open AI into a table
- Using the tables , Power BI Visuals are created to gain insights

## Steps 

**Create Function App as a wrapper to execute and process data from OpenAI**

- Create a  C# Azure Function from Visual Studio , Refer  [here](https://learn.microsoft.com/en-us/azure/azure-functions/functions-develop-vs?tabs=in-process) to
  Create an Azure Functions project

- In Azure Open AI Create a GPT 3.5 deployment 

   <img width="864" alt="image" src="https://github.com/mahes-a/Wingman/assets/120069348/dd9b5f1e-7ee2-47aa-9ade-bd2c750ffa62">

 - Note down the OPENAI_API_KEY and endpoint, they can be retrieved from Azure Portal Azure OpenAI resource under Keys and Endpoint section
    
  <img width="620" alt="image" src="https://github.com/mahes-a/Wingman/assets/120069348/646b0ebf-bf99-45cd-bc90-ee0fcf384b1c">

  - In your VS code or Visual studio , Add the Open AI endpoint , deployment name , keys to the local.settings.json file
    
    <img width="416" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/310a94eb-c7b8-4eab-b502-20ddaa3cc373">

  - Set the prompts recieved from HTTP Post body to variables for further processing
    
    <img width="529" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/c34777b0-cc2c-48bb-8a39-93f2fb4f6cbc">
  
  - Install the Azure Open AI.NET client library , Refer [here](https://learn.microsoft.com/en-us/dotnet/api/overview/azure/ai.openai-readme?view=azure-dotnet-preview) for installation instructions
    
  - Next step is to pass the prompt receieved from HTTP post method to execute the Open AI ChatCompletion API and return the response.

    <img width="884" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/45e787bf-944e-4ba9-b296-007467e2ae69">

  - One easy way to get the code for the Azure Open AI API calls is to get the sample code from your Azure AI Studio, Go to Azure AI Studio-> Playground -> Chat -> View         code from Chat Session section
     
     <img width="866" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ec3f13f8-bac9-4a16-b69a-26d80921ab6b">

  - Build , Run the solution to get the results in Json Format, Refer below sections on  prompt designs to enable Open AI Api provide only Json Array as output. Note down the function app url

    <img width="401" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f44a3f2e-96b9-4943-b66f-68d84ce55f24">


**Prompt Engineering to produce Json Array from Azure Open AI API**

   - ff
  - For this example , the prompt is engineered in a way OpenAI returns the results only in a Json Array without any other qualifying perfix/suffixes 
  - d
