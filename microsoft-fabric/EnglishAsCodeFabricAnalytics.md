# End-to-End Big Data Analytics with English as Programming Language for Apache Spark in Microsoft Fabric, Powered by Azure Open AI

Let English be your programming language! With Microsoft Fabric, Azure Open AI, and English SDK for Apache Spark, you can convert simple English instructions to big data transformations and accelerate your time to insights with Code-Free Data ingestion and automatic report creation. Empower your big data transformations with English and revolutionize the way you analyze data!

## High-level Architecture

<img width="728" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/7942c343-fb9e-4150-8155-44ccba673d9f">


## Prerequisites

- Azure subscription with access enabled for the Azure OpenAI service. You can request access [here](https://aka.ms/oaiapply). 
- Microsoft Fabric You can enable free trial [here](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial).
- Microsoft OneLake file explorer for Windows , Download [here](https://www.microsoft.com/en-us/download/details.aspx?id=105222)

## Getting Started

- The English SDK for Apache Spark takes English instructions and compiles them into PySpark objects like DataFrames using the Power of LLM.

  - The English SDK for Apache Spark aims to simplify the use of Spark and make it more accessible to users with varying levels of technical expertise. By enabling users to provide instructions in plain English, the tool allows them to focus on the analysis and interpretation of their data, rather than on the complexities of the underlying code. This can help users to gain insights more quickly and effectively, accelerating their data-driven decision-making processes.
  
- The English SDK for Apache Spark is developed as part of Databricks Labs projects , The entire code for this project is available on GitHub and is licensed under the Apache 2.0 license. The repository has multiple contributors and contains all the source code, functions, and documentation needed to use the SDK.  Check it out here: https://github.com/databrickslabs/pyspark-ai/
  


## Things to Note 

- Please note that this tutorial with English instructions being converted into Pyspark Dataframe is intended for explorative and illustrative purposes only. It is meant to inspire ideas and should not be taken as prescriptive advice. Any implementation of the techniques described in this tutorial as part of your application should be thoroughly validated and tested to ensure accuracy, validity, compatibility with your specific use case and technical environment.
  
- Please be aware that the English prompt instructions shown are not intended to be a correct and complete representation of the prompts that should be used with your applications in production. They are provided for informational purposes only and may not be suitable for all use cases. It is important to carefully consider your specific requirements and design appropriate prompts that meet your users' needs and expectations.
  
- While LLMs have tremendous potential across many industries and use cases, it is essential to ensure that they are built in a safe and responsible manner. This includes taking steps to mitigate potential risks and ensure that the model will not cause harm to users or result in reputational damage to organizations. It is important to carefully consider the ethical implications of LLMs and to develop appropriate safeguards to protect against potential harms.
  
- Refer [here](https://learn.microsoft.com/en-us/legal/cognitive-services/openai/data-privacy) for details regarding how data provided by you to the Azure OpenAI service is processed, used, and stored
  
- The English SDK for Apache Spark has Data Ingestion capabilities to ingest data directly from bing and other search API's , We will focus on the data transformation feature of the SDK in this tutorial

- Fabric provides many ways to ingest data (Pipelines, Dataflow,Shortcuts,uploading files, File sync) , we will use OneLake file explorer sync for ingestion in this tutorial
  
- In this example , We use SQL Endpoint of the Lakehouse to model data and create a dataset to show case the feature of auto creating a report (and pre-selecting your data) from dataset , there are many other options in fabric to create reports
  
- Refer below for decision guides for capabilities and use cases

  https://learn.microsoft.com/en-us/fabric/get-started/decision-guide-warehouse-lakehouse

  https://learn.microsoft.com/en-us/fabric/data-warehouse/data-warehousing#compare-different-warehousing-capabilities

## Technical Flow

- In this sample, Source data is copied into the OneLake File Explorer there by automatically syncing the files to your Fabric workspace lakehouse
  
- In Microsoft Fabric notebook , the source files are read and transformed using English instructions to craete a Star schema model and the model is persisted in lakehouse  
  
- Using the SQL Endpoint for the lakehouse relationship between fact and dimension tables are established and a dataset is created selecting only the required tables
  
- From the dataset , Reports are auto created. 
  
-  
