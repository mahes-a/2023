# Microsoft Fabric : No-Code Alerting on Real-time Streams using Data Activator

Data Activator is a no-code tool part of Microsoft Fabric that allows users to automatically take actions based on  conditions and patterns in source data. It monitors data in Power BI reports and Event Hub data streams, triggering actions such as alerts or workflows. It enables businesses to build a alerting system that can handle data at scale, reducing reliance on IT or developer teams. This tool eliminates the need for manual monitoring and offers a cost-effective, no-code solution for acting on data insights.

Refer [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md) and [here](https://blog.fabric.microsoft.com/en-us/blog/driving-actions-from-your-data-with-data-activator/) for more info.

## High-level Architecture
<img width="738" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/e0784555-a03d-4f1c-ac0f-3f30fb126a38">

## Prerequisites

- Azure subscription with Azure Event Hub resource. 
- Microsoft Fabric You can enable free trial [here](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial).
- Data Activator is in private preview , you can request access [here](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbRzMF5NSZnI1CrCTbAYcvUW9UM1NWWUUzVlpEVVZJS0lSM1ZWUDBSTkMyVi4u)
- Refer [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md#faq-things-to-know-before-using-data-activator) for things to know before using Data Activator
- Visual Studio

## Technical Flow

- In this sample, A sample .NET console app  reads subset of  NYC Taxi Dataset at pre-set intervals and Send events to Azure Event Hubs
  
- In Data Activator gets data from Azure Event Hub , creates trigger based on data patterns and sends email alerts when data patterns are matched
  
  - Using visual options , when payment type of the streamed taxi data is of type "dispute" emails are sent
  
