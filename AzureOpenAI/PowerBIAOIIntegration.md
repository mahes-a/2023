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

- In this sample, we build an Azure Function App which receives prompts from Power BI, executes the Azure Open AI API and responds with data results from Azure Open AI in a JSON format
- Power Query (within Power BI) will make Http Post calls to the function App with the prompts and transforms the results from Azure Open AI into a table
- Using the tables , Power BI Visuals are created to gain insights  
