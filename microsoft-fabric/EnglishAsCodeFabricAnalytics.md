# End-to-End Big Data Analytics with English as Programming Language for Apache Spark in Microsoft Fabric, Powered by Azure Open AI

Let English be your programming language! With Microsoft Fabric, Azure Open AI, and English SDK for Apache Spark, you can convert simple English instructions to big data transformations and accelerate your time to insights with low-code/no-code data ingestion and automatic report creation. Empower your big data transformations with English and revolutionize the way you analyze data!

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
  
- In this example, We use SQL Endpoint of the Lakehouse to model data and create a dataset to show case the feature of auto creating a report (and pre-selecting your data) from dataset , there are many other options in fabric to create reports
  
- Refer below for decision guides for capabilities and use cases for Fabric lakehouse , sql end point and ware house

  https://learn.microsoft.com/en-us/fabric/get-started/decision-guide-warehouse-lakehouse

  https://learn.microsoft.com/en-us/fabric/data-warehouse/data-warehousing#compare-different-warehousing-capabilities

## Technical Flow

- In this sample, Source data is copied into the OneLake File Explorer there by automatically syncing the files to your Fabric workspace lakehouse
  
- In Microsoft Fabric notebook, the source files are read and transformed using English instructions to create a Star schema model and the model is persisted in lakehouse  
  
- Using the SQL Endpoint for the lakehouse relationship between fact and dimension tables are established and a dataset is created selecting only the required tables
  
- From the dataset, Reports are auto created. 
  
## Steps 

**Low-code/No-code data ingestion Data ingestion -- Create a Fabric Lakehouse and copy source files using OneLake File Explorer**

- Login into Fabric portal [here](https://app.fabric.microsoft.com/) and choose Data Engineering persona
  
   <img width="244" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/c2e9fdfb-7f50-4612-b096-83d473a35e58">

    
 - Create a Lakehouse or select your existing lakehouse

   <img width="491" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8913d2f8-d253-4314-a289-317e57776f31">

 - This tutorial uses NYC green taxi trip data parquet files and Taxi Zone Lookup Table (CSV) file, Find them [here](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

- Download the source files in your system and copy them into your OneLake-> workspace->Your Lakehouse -> Files - location using the OneLake file explorer

  <img width="605" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/992ea513-1e43-4a0b-bfd9-a6fcfb562f6f">

- Switch back to your Lakehouse explorer in fabric and validate all the files are now synced. Please use refresh option if you don’t see the files
  
   <img width="937" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/43ab5075-62aa-4638-a901-aa992ca1b33f">

**Using English as a programming language for big data transformations**

- Open a New notebook
  
   <img width="525" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8fdea60b-b85e-408d-8678-5d78436faa35">

- Install the required python libraries through in-line installation (in-line for simplicity) , Refer [here](https://learn.microsoft.com/en-us/fabric/data-engineering/library-management) all the other options for installing libraries on your workspace
  
  <img width="870" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/9edf0917-be3d-4109-aa71-a50e28fe2918">

- Optional vorder enabling and optimizeWrite , Refer [here](https://learn.microsoft.com/en-us/fabric/data-engineering/delta-optimization-and-v-order?tabs=sparksql) to understand more on the optimizations
  
  <img width="893" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/258067c3-1e4f-4977-ba63-5926c07adf2e">

- Import the reference packages
  
  <img width="866" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/07103c01-9244-47da-b7e0-a2984cec6a1f">

- Setup your AzureChatOpenAI function with Azure open ai endpoint, Azure open ai key , deployment name (use GPT 3.5 turbo or GPT 4 models) , Follow below for creating and retrieving the required details

  <img width="467" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/c2955cb0-3b88-47ab-a089-d106a6fdd52a">

- For Example , In Azure Open AI Create a GPT 3.5 deployment 

   <img width="864" alt="image" src="https://github.com/mahes-a/Wingman/assets/120069348/dd9b5f1e-7ee2-47aa-9ade-bd2c750ffa62">

- Note down the OPENAI_API_KEY and endpoint, they can be retrieved from Azure Portal Azure OpenAI resource under Keys and Endpoint section
    
    <img width="620" alt="image" src="https://github.com/mahes-a/Wingman/assets/120069348/646b0ebf-bf99-45cd-bc90-ee0fcf384b1c">

- Initialize the English SDK for Apache Spark by ensuring it uses the Azure Open AI models instantiated in above step , By enabling the verbose option, the English SDK provides visibility into the underlying SQL operations used to create DataFrames from English prompts. This can help to validate the process and prevent errors, while also introducing an additional safety measure by allowing users to review the tool's internal operations.
    
   <img width="712" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/1ebc5e28-7c94-4f92-b153-93d9f4dbf54c">

- Read the source files into dataframes

  <img width="758" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8fc14024-c0aa-47f9-9822-5889c0c54a04">

**Let's begin data transformations in English!**

- By providing English instructions , we select only the required columns and add date column from timestamp columns , Since we enabled verbose mode underlying sql query for the requested data transformation is displayed , validate the query

  <img width="880" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/636f4209-c571-418c-89d3-543102534184">

- Once SQL query is validated , we could use display function to visually validate the results

   <img width="664" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/3f572d87-28d9-439b-acaa-56b8a44890f6">

   <img width="860" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/5c6b8579-4b0a-4511-ba21-82b7ed6b4c16">


- Let's add a surrogate key to the lookup table by providing English instruction and repeat the same process of validating sql query and visually validating the results

  <img width="821" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/2d517e4d-ab2d-47a3-892f-6fc7c2f56b60">


- Let's create role playing dimension table for pickup and dropoff location details by providing English instructions from the lookup table , validate the sql query and data before writing to table

   <img width="835" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/dcb39606-e26b-4d84-b92d-245443c0ab90">


-  By providing English instructions let's create a fact table for taxi trips using the surrogate key from the dimension tables and write to table after validating the sql query and data

  <img width="866" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ff3f5eca-68a5-40ce-85d7-3aff0757315b">


- To ensure safety , let's validate the dataframe for any duplicates by providing English instruction , this adds a additional layer of validation before writing to table

    <img width="659" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/2f00d883-dc92-4feb-a3f6-a5cbbe755496">
    

**Auto-create Reports -- Data Model Star Schema dataset and auto-create reports**

- In the Lakehouse explorer switch to Sql end point. Select the model tab from left bottom 
        
     <img width="290" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/384a3c07-0b3b-4e8f-8abc-8707b3b2a99d">

- For this data model, you need to define the relationship between different tables so that you can create reports and visualizations based on data coming across different tables. From the fact_taxitrip table, drag the pickup_location_surrogate field and drop it on the pickup_location_surrogate field in the dim_pickuplocs table to create a relationship. Check the “Assume referential integrity” and select Confirm to establish the relationship. Note - When defining relationships, make sure you have many to one relationship from the fact to the dimension and not vice versa , repeat the same for dropoff_location_surrogate from fact_taxitrip to dropoff_location_surrogate in dim_dropofflocs table

  <img width="557" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/88b098ae-8af2-4c12-8654-668f84b6fc25">
  

  <img width="556" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/cad09d00-eeab-4a92-812e-35f03f8853ec">

-  On the reporting tab select "New Power BI dataset" , provide dataset name and select the fact and dimension table

   <img width="898" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8a4c13bc-69dc-4e6a-8235-f9f5a9138646">
   
  
   <img width="445" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/4202709f-1999-4ee6-aa12-bc549d390fcf">

   
 - Rename the dataset to your liking 

   <img width="419" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/cc6169c9-9e0a-4a91-bf56-3c104aa9be00">

- Go to your workspace and open the datset created and select Auto create report and pre- select the dimesnion and fact fields to create the report. Save the report 


  <img width="1116" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8beb9c0c-7d90-4a40-aa1e-117be7435f55">

  
  
  <img width="805" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d4843d68-3f1b-491f-ae5b-66ba4ab907c1">


  <img width="1138" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/e906eec0-1398-46f0-b53d-297b7e9e224e">

  
