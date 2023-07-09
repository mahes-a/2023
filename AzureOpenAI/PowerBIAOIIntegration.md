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

## Things to Note 

- The Function App acts as a wrapper, facilitating the use of other tools such as API management gateway, to enable load balancing, scaling out, and overcoming token limitations by executing  multi-region Azure Open AI resources (if regulations permit) in a straightforward round-robin fashion., Furthermore, Function App with APIM can assist with chargebacks, auditing, monitoring, and provide a secure abstraction layer.
  
- Function App becomes a facade serving layer and provides simple data integration end points for any client tool.
  
- In this example , we generate customer feedback samples Open AI with customer sentinment for simplicity but this can be designed in a way that true customer feedback can be sent to Open AI to perform sentiment analysis
  
- 

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

  - For this example , the prompt is engineered in a way OpenAI returns the results only in a Json Array without any other qualifying prefix/suffixes
    
  - This can be done providing proper prompt tempalate consisting of System messages , Output format , Providing examples . Refer [here](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/system-message) for Prompt Engineering tips

 - Some samples for prompts to get only Json array from the Azure Open AI interface can be
      
         Generate Json array with 25 elements with sentiment analysis, you will respond with the sample as a JSON object. Here's the output format [{ "CustomerFeedback": " I-love-the-product", "Sentiment": "Positive" ,"Score":".9"},{ "CustomerFeedback": " I-hate-the-product", "Sentiment": "Negative" ,"Score":".9"}]  

         Only list  top country names by Population do not respond with any other response rather than the country and Population, Do not include any explanations, only provide a  RFC8259 compliant JSON response following this format without deviation. [{"Country":"USA","Population":"12222"},{"Country":"China","Population":"333"}]   
  
**Power Query Post to Function App and transformations**

  - Open a Power BI Desktop , Click on Transform Data-> Transform Data from home ribbon

  <img width="827" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a1fd0cbe-8aa5-4a3f-9de4-4a04f4c1b6b3">

- Open a Power BI Desktop , Click on Transform Data-> Transform Data from home ribbon

  
- In the Power Query editor from Manage parameters , add new parameter
    
    <img width="1193" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/c344ec68-b6de-4b4f-8532-5bc9839366f7">

- In the Power Query editor from Manage parameters , add new parameter URL and provide the function app url
    
    <img width="479" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/6f7c2c66-2969-4cbd-a272-c698d1502f95">
    
- Repeat by adding Parameters for the prompts to be passed , The customer sentiment analysis prompt is provided in above section
     <img width="461" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/4111063f-9728-4721-a801-5fd953a50d60">

-  From New Source ribbeon , select "Blank Query"

   ![image](https://github.com/mahes-a/2023/assets/120069348/070ef26a-6874-4ba1-ba19-9490fb94a744)

- Once the query opens , use the Advanced editor to open the Advanced Editor window
  
    <img width="379" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8336fa5d-be7c-4a76-8078-8d3b51feba5c">

- We will code to make the post calls to the Function app from the window , we use the prompt parameter and function app url to make the post call and click done
  
    <img width="756" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a41752a0-da46-4ac3-8b57-09d0362ca71f">
 
- The Power query editor will list all the Json documents from the Azure Open AI API result via the function app
  
    <img width="521" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/2cbf050a-2d30-4560-a38a-436c56b7a046">
 
- Select "To table"  to convert the Json documents to table

     <img width="599" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/76bc57bf-d7c7-4bfa-8a68-a5121d68b107">

- All the Json records would be displayed now

    <img width="545" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/1bdbadb7-83cb-43e0-b2d0-2971be7dc248">

- Click the expand colum icon to expand all the records to table columns

    <img width="208" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/b54fe3c2-9c3c-45f1-a62a-db1dce425cf5">
  
- The Json elements retrieved are now avialable in Tabular format  

  <img width="877" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/877da3b3-1d94-45a1-8c3b-b85d5e90c968">

- Click "Close and apply" from the menu to close power query editor and apply changes on the Power BI to make the data model available for visualization
  
   <img width="305" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d9dbc0fe-963d-423a-9a10-8ea95017705d">
 
- Now create visuals to to provide insights to users

  <img width="1058" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/6bfe6b0c-792f-49e1-93ac-924c57d1ea2f">

- f
- f
- 

   - 
    



    

