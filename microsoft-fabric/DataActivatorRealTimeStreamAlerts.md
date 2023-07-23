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

- A sample .NET console app  reads subset of  NYC Taxi Dataset at pre-set intervals and sends events to Azure Event Hubs
  
- Data Activator gets data from Azure Event Hub , creates trigger based on data patterns and sends email alerts when data patterns are matched
  
  - Using visual options , when payment type of the streamed taxi data is of type "dispute" emails are sent

## Steps 

**Create an event hub using Azure portal**

- Follow the steps listed [here](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-create) to create a Azure Event Hub Resource from Azure Portal
  
- For simplicity , We will use the connection string of event hub resource for authentication in this sample , Please consider using Passwordless (Azure Active Directory authentication) for more secure applications . Refer [here](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-get-connection-string?source=recommendations#connection-string-for-a-specific-event-hub-in-a-namespace) to get the connection string for the event hub resource

**Create an .NET Console App to send data to eventhub**
- Follow the steps listed [here](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send?tabs=connection-string%2Croles-azure-portal) to setup the C# console app to send the events to eventhub created as part of above step, we will use the connection string option for event hub authentication
  
- Please note , we would be using the .NET app to send events and would not be recieing events from event hub , so please exclude Receive events from the event hub section in the above link

- <img width="625" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/69280b6b-3c51-45b5-8412-503cf8bf5a22">

-  
- 
