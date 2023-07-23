# Microsoft Fabric : No-Code Alerting on Real-time Streams using Data Activator

Data Activator is a no-code tool part of Microsoft Fabric that allows users to automatically take actions based on  conditions and patterns in source data. It monitors data in Power BI reports and Event Hub data streams, triggering actions such as alerts. It enables businesses to build a alerting system that can handle data at scale, reducing reliance on IT or developer teams. This tool eliminates the need for manual monitoring and offers a cost-effective, no-code solution for acting on data insights.

Refer [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md) and [here](https://blog.fabric.microsoft.com/en-us/blog/driving-actions-from-your-data-with-data-activator/) for more info.

## High-level Architecture
<img width="734" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/c51da9f3-f35d-40b4-8249-9620ac5b1088">


## Things to Note 
- Microsoft Fabric is in public preview and Data Activator is in private preview , Please read [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md) and [here](https://blog.fabric.microsoft.com/en-us/blog/driving-actions-from-your-data-with-data-activator/) for complete info including FAQ's , Prerequisites , performance guidelines 
  
- The tutorial uses NYC Green Taxi Dataset , Available [here](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page) in Parquet format and CSV format dataset can be found Kaggle , The tutorial uses CSV format , Please convert Parquet to CSV format or use csv format dataset from Kaggle
  
- The tutorial uses a .NET Console app that mimics taxi Streaming device by sending sample rows from NYC Green Taxi Dataset at preset intervals
  
- Please note that this tutorial is intended for explorative and illustrative purposes only. It is meant to inspire ideas and should not be taken as prescriptive advice. Any implementation of the techniques described in this tutorial as part of your application should be thoroughly validated and tested to ensure accuracy, validity, compatibility with your specific use case and technical environment.

- Data Activator can have Power BI report as source , we focus here on Azure EventHub real time streaming alerts in this article

## Prerequisites

- Azure subscription with Azure Event Hub resource. 
- Microsoft Fabric You can enable free trial [here](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial).
- Data Activator is in private preview , you can request access [here](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbRzMF5NSZnI1CrCTbAYcvUW9UM1NWWUUzVlpEVVZJS0lSM1ZWUDBSTkMyVi4u)
- Refer [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md#faq-things-to-know-before-using-data-activator) for things to know before using Data Activator
- Visual Studio

## Technical Flow

- A sample .NET console app reads subset of  NYC Taxi Dataset at pre-set intervals and sends events to Azure Event Hubs
  
- Data Activator gets data from Azure Event Hub , creates trigger based on data patterns and sends email alerts when data patterns are matched
  
  - Using visual options , when payment type of the streamed taxi data is of type "dispute" emails are sent

## Steps 

**Create an event hub using Azure portal**

- Follow the steps listed [here](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-create) to create a Azure Event Hub Resource from Azure Portal
  
- For simplicity , We will use the connection string of event hub resource for authentication in this sample , Please consider using Passwordless (Azure Active Directory authentication) for more secure applications . Refer [here](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-get-connection-string?source=recommendations#connection-string-for-a-specific-event-hub-in-a-namespace) to get the connection string for the event hub resource

**Create an .NET Console App to send data to EventHub**

*Please note this sample console app tries to mimic a streaming app by just sending few rows to EventHub at pre-configured intervals, the code does not handle errors , nor production grade authentication and does not guarantee uniqueness of records being sent, this is created just for demo purposes only*

- Follow the steps listed [here](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send?tabs=connection-string%2Croles-azure-portal) to setup the C# console app to send the events to EventHub created as part of above step, we will use the connection string option for event hub authentication
  
- Please note, we would be using the .NET app to send events and would not be receiving events from event hub , so please exclude Receive events from the event hub section in the above link

  <img width="413" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/3eb8d1fe-b13a-44b0-aa5e-cbba8e9d6f39">

- For more details , you could watch "AZ-204 - Connect and Consume Services - Azure Event Hubs" YouTube video by Alan Rodrigues  [here](https://www.youtube.com/watch?v=HwZldR8KlKM)

-  Copy the NYC Green Taxi dataset in CSV format into the Console app and ensure "Copy always" option is selected for the file in Copy to output directory in the file properties.
  
      <img width="267" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/62fa4d2e-c150-43ff-8d72-2ca7718324fd">

-  In the Console App, Add a class to represent the NYC Green Taxi dataset , we are adding the override string ToString() method to return Json object when ToString() is called , this is one way to ensure Json formatted data is sent to EventHub. 
      ```
      using System.Text.Json;
  
  
      namespace SendToAzureEventHub
      {
          internal class NycGreenTaxiData
          {
              public int VendorID { get; set; }
              public DateTime lpep_pickup_datetime { get; set; }
              public DateTime lpep_dropoff_datetime { get; set; }
              public string store_and_fwd_flag { get; set; }
              public string RatecodeID { get; set; }
      
              public string PULocationID { get; set; }
      
              public string pickup_borough { get; set; }
      
              public string pickup_zone { get; set; }
      
              public string pickup_service_zone { get; set; }
      
              public string DOLocationID { get; set; }
      
              public string dropoff_borough { get; set; }
      
              public string dropoff_zone { get; set; }
      
              public string dropoff_service_zone { get; set; }
      
              public string passenger_count { get; set; }
      
              public double trip_distance { get; set; }
      
              public double direct_distance { get; set; }
      
      
              public double fare_amount { get; set; }
              
              public double extra { get; set; }
      
              public double mta_tax { get; set; }
      
              public double tip_amount { get; set; }
      
              public double tolls_amount { get; set; }
      
              public string ehail_fee { get; set; }
      
              public double improvement_surcharge { get; set; }
      
              public double total_amount { get; set; }
      
              public string payment_type { get; set; }
      
      
              public string trip_type { get; set; }
      
      
              public string congestion_surcharge { get; set; }
      
              public override string ToString()
              {
                  return JsonSerializer.Serialize(this);
              }
      
          }
      }


- Add the CsvHelper package via Nuget, Refer [here](https://www.nuget.org/packages/CsvHelper/)
  
- In the Console App , Modify the Program.cs file as below to send events to EventHub
  
       
        using Azure.Messaging.EventHubs.Producer;
        using CsvHelper;
        using System.Globalization;
        
        namespace SendToAzureEventHub
        {
            internal class Program
            {
                public static List<NycGreenTaxiData> taxiDataLst = new List<NycGreenTaxiData>();
                public static List<NycGreenTaxiData> taxiDataLstOut = new List<NycGreenTaxiData>();
                public static List<NycGreenTaxiData> sendToHub = new List<NycGreenTaxiData>();
                static void Main(string[] args)
                {
                    //read data from csv file
                    PrepStream();
                    //stop after 10 calls if left running
                    for (int i = 0; i < 10; i++)
                    {
                        //send to event hub
                        SendEvents();
                        //sleep for 6 seconds before sending events again
                        Thread.Sleep(6000);
                    }
        
                }
        
                static void SendEvents()
                {
                    Random random = new Random();
                    //return random index between zero and 50 records less than total rows in dataset , no check for exceptions done
                    int minnum = random.Next(0, taxiDataLstOut.Count() - 50);
                    //get 50 random rows to be sent to eventhub based on index , doesnot do duplicate checks reads 50 rows at random , no check for exceptions done
                    sendToHub = taxiDataLstOut.GetRange(minnum, 50);
        
        
                    //connection string to eventhub
                    EventHubProducerClient _client = new EventHubProducerClient("your eventhub connection string here");
                    EventDataBatch _batch = _client.CreateBatchAsync().GetAwaiter().GetResult();
                    //send each row as a event to eventhub , ToString() creates Json format , log the rows sent
                    foreach (NycGreenTaxiData data in sendToHub)
                    {
                        _batch.TryAdd(new Azure.Messaging.EventHubs.EventData(System.Text.Encoding.UTF8.GetBytes(data.ToString())));
                        Console.WriteLine(data.ToString());
        
                    }
                    //send the events and confirm sucess , no exception handling done here
                    _client.SendAsync(_batch).GetAwaiter().GetResult();
                    Console.WriteLine("Events Sent");
                }
        
                static void PrepStream()
                {
        
                    var rootDir = System.IO.Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location);
        
                    //Change the file path if needed , added the file cia copy always option
                    using (var reader = new StreamReader(@rootDir + "\\GreenTaxidataenh.csv"))
                    using (var csv = new CsvReader(reader, CultureInfo.InvariantCulture))
                    {
                        taxiDataLst = csv.GetRecords<NycGreenTaxiData>().ToList();
                    }
                    
                    taxiDataLstOut = taxiDataLst.ToList();
        
                }
            }
        }
- Add your event hub connection string in EventHubProducerClient _client = new EventHubProducerClient("your eventhub connection string here"); section
  
- Build the solution and run the console app , close the app after confirming the events are sent , please note the app sends 50 events every 6 seconds and does 10 loops , stop after seeing events sent message

  <img width="860" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8424e2df-874e-4727-9d65-c66d48b8143c">

**Create No-code Data Activator to send alerts from Microsoft Fabric**

- Login into Fabric portal [here](https://app.fabric.microsoft.com/) and choose Data Activator persona , This assumes you have signed up for the private preview of Data Activator

  <img width="259" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d590d94f-452d-402d-9166-2fc2144579b8">
  
- Create a Reflex item
  
  <img width="452" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f10fb1f0-f611-4163-86fa-8ae21f6cd9ca">

- Click on Get Data and enter a name, Event hub connection string and leave Consumer group as $Default and Time field as empty 

   <img width="979" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a4adc9b0-03d1-456d-a2ba-7cc841e4ba20">
   
    <img width="454" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/e684a67a-5962-43e3-89ff-2ff0dd8aa0b7">

- The data would be loaded from the event hubs

   <img width="919" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d0323cd9-8fc4-4387-8457-6f813cec02bf">

   <img width="1093" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/afc90d47-5593-4457-8252-ec9c65577ad8">

- We will need at least one of the keys in the event hub Json to uniquely identifies an object. This can be something like device id , package id or taxi meter id , we do not have a taxi meter id in our dataset and we did not send unique events to EventHub in this demo  but we will use Signal.Id event hub event data property that identifies unique event through Guid, In real-world applications , there would be unique id sent from the signal , it may be tracking id , package id , device id etc., 

- As we mimic the data streaming , we don’t have a unique id in our demo and are using the event hub internal property of Signal.Id , this would not be the case in real-world streaming apps.

- Click on three dots on the Signal.ID property and add create object and name the object 

<img width="329" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/4122b08a-ffa9-4d3a-840d-13909460d504">


<img width="259" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/2333c50d-0847-4138-8106-0d3374e0ffe8">

- Creating the object would open up the design tab with 5 signal id's selected as reference and available in the graph , The following image shows you the events that were received split out by the unique values from the signal ID column.

 <img width="1127" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d5d60bae-14dc-4e5f-ab9a-c375b943f938">

   
**Add property to track and create alerts**

- Click on new property from the top that you want to monitor and respond to.

  <img width="1109" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ce617322-86ca-4d68-8c55-911ec6621b97">

- For our case , we will monitor the payment_type property ,Name the property accordingly  


  <img width="1092" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/3983df96-6556-4c96-8465-52f0c85e8101">

  <img width="269" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/3c1bd747-6af6-4db4-a866-8fd94be1b240">

  <img width="921" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/b0d14c3f-a489-4f14-9fe0-2c531ba134a0">

- From the NYC Green Taxi data dictionary, below are the payment type descriptions for each number

    <img width="519" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ae118fd8-d73a-4de2-8a57-579fdb4b185c">

- We will monitor the payment_type and create and send alerts when payment_type is 4 that is dispute , this is done by clicking on detect and add is equal

    <img width="1121" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/e6a8dd5f-7aa5-4994-8915-0554f8173bb2">

- Enter the value as 4 in the value field , the value of 4 is represented in the graph , if you do not see value in the graph then chose the signal id which has payment_type of 4 by looking up the value from data tab, when selecting the data tab , save the progress 

  <img width="889" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/59c4a7e3-11ec-4ddc-ab65-60cf73047ff4">

  <img width="631" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f00da54c-dcfa-433c-b412-c194ff7bf70a">
  
  <img width="901" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/bdb239c8-074e-402e-9641-9bd3d102d062">

  <img width="922" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/68d87469-fb82-4b26-8a1a-14bb19c0c3ef">

  <img width="932" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/55e1c0d8-08e5-44fc-80e9-7b4f85fed3d4">

 - If you don’t see value of 4 in your data, we could stream more data from the console app or use a available payment_type or other value for alerting

 - In this detect mode, we are tracking at every event the has payment_type as dispute. Lets change the every time option to Number of times and establish the window of time , for example detect when Payment_type of 4 (dispute) happens 5 times over 1 minute 

   <img width="929" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/6f8c4e77-d3db-4fe4-9668-e2f4bae34653">

 
 - In the next step click on Act and send email to create a trigger

   <img width="913" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f93ee1b6-0c3d-4738-8cc0-e8cde9cb9bdb">
   
 - From the send email section , we can click on "Send email to me" to send a test notification 

   <img width="916" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/dbe0ad1d-65d9-43f5-a38b-a9f2d1ed04d8">

   <img width="790" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f1e30af9-b3f5-4c57-afdc-948782715e01">

  
 - Below is the one sample email recieved

    <img width="974" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/61ba2a2a-b53f-44ce-9a8d-9cda975c4ccc">


- We could update subject , headline , optional message to  better match the alerting conditon  

   <img width="904" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/261fdf89-f007-4982-a2c8-c62fd0547d96">

- We could update subject , headline , optional message to  better match the alerting condition and click start 
 
    <img width="914" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/857e1718-6317-4a8e-b225-83fc63ca0800">

- We could stream the data using console app and whenever the condition of when Payment_type of 4 (dispute) happens 5 times over 1 minute the emails would be sent. Please ensure set the alert criteria  higher (like 100 times over 1 minute) to prevent inbox being flooded with alert emails when condition is met
  
    <img width="664" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/af3056b3-2ac8-43c1-8b20-edf65d043c45">

