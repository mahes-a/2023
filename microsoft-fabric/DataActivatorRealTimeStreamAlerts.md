# Microsoft Fabric : No-Code Alerting on Real-time Streams using Data Activator

Data Activator is a no-code tool part of Microsoft Fabric that allows users to automatically take actions based on  conditions and patterns in source data. It monitors data in Power BI reports and Event Hub data streams, triggering actions such as alerts or workflows. It enables businesses to build a alerting system that can handle data at scale, reducing reliance on IT or developer teams. This tool eliminates the need for manual monitoring and offers a cost-effective, no-code solution for acting on data insights.

Refer [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md) and [here](https://blog.fabric.microsoft.com/en-us/blog/driving-actions-from-your-data-with-data-activator/) for more info.

## High-level Architecture
<img width="734" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/c51da9f3-f35d-40b4-8249-9620ac5b1088">


## Things to Note 
- Microsoft Fabric is in public preview and Data Activator is in private preview , Please read [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md) and [here](https://blog.fabric.microsoft.com/en-us/blog/driving-actions-from-your-data-with-data-activator/) for complete info including FAQ's , Prerequisites , performance guidelines 
  
- The tutorial uses NYC Green Taxi Dataset , Available [here](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page) in Parquet format and CSV format dataset can be found Kaggle
  
- The tutorial uses a .NET Console app that mimics taxi Streaming device by sending sample rows from  NYC Green Taxi Dataset at preset intervals
  
- Please note that this tutorial  is intended for explorative and illustrative purposes only. It is meant to inspire ideas and should not be taken as prescriptive advice. Any implementation of the techniques described in this tutorial as part of your application should be thoroughly validated and tested to ensure accuracy, validity, compatibility with your specific use case and technical environment.

## Prerequisites

- Azure subscription with Azure Event Hub resource. 
- Microsoft Fabric You can enable free trial [here](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial).
- Data Activator is in private preview , you can request access [here](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbRzMF5NSZnI1CrCTbAYcvUW9UM1NWWUUzVlpEVVZJS0lSM1ZWUDBSTkMyVi4u)
- Refer [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md#faq-things-to-know-before-using-data-activator) for things to know before using Data Activator
- Visual Studio

## Technical Flow

- A sample .NET console app  reads subset of  NYC Taxi Dataset at pre-set intervals and Send events to Azure Event Hubs
  
- In Data Activator gets data from Azure Event Hub , creates trigger based on data patterns and sends email alerts when data patterns are matched
  
  - Using visual options , when payment type of the streamed taxi data is of type "dispute" emails are sent
  
