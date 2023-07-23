# Microsoft Fabric : No-Code Alerting on Real-time Streams using Data Activator

Data Activator is a no-code tool part of Microsoft Fabric that allows users to automatically take actions based on  conditions and patterns in source data. It monitors data in Power BI reports and Event Hub data streams, triggering actions such as alerts or workflows. It enables businesses to build a alerting system that can handle data at scale, reducing reliance on IT or developer teams. This tool eliminates the need for manual monitoring and offers a cost-effective, no-code solution for acting on data insights.

Refer [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md) and [here](https://blog.fabric.microsoft.com/en-us/blog/driving-actions-from-your-data-with-data-activator/) for more info.

## High-level Architecture
<img width="734" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/c51da9f3-f35d-40b4-8249-9620ac5b1088">


## Things to Note 
- Microsoft Fabric is in public preview and Data Activator is in private preview , Please read [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md) and [here](https://blog.fabric.microsoft.com/en-us/blog/driving-actions-from-your-data-with-data-activator/) for complete info including FAQ's , Prerequisites , performance guidelines 
  
- The tutorial uses NYC Green Taxi Dataset , Available [here](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page) in Parquet format and CSV format dataset can be found Kaggle , The tutorial uses CSV format , Please convert Parquet to CSV format or use csv format dataset from Kaggle
  
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

*Please note this sample console app tries to mimic a streaming app by just sending few rows to eventhub at pre-configred intervals, the code doesnot handle errors , nor production grade authencitaion and doesnot guarantee uniqueness of records being sent, this is created just for demo purposes only*

- Follow the steps listed [here](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send?tabs=connection-string%2Croles-azure-portal) to setup the C# console app to send the events to eventhub created as part of above step, we will use the connection string option for event hub authentication
  
- Please note , we would be using the .NET app to send events and would not be recieing events from event hub , so please exclude Receive events from the event hub section in the above link

  <img width="413" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/3eb8d1fe-b13a-44b0-aa5e-cbba8e9d6f39">

- For more details , you could watch "AZ-204 - Connect and Consume Services - Azure Event Hubs" youtube video by Alan Rodrigues  [here](https://www.youtube.com/watch?v=HwZldR8KlKM)

-  Copy the NYC Green Taxi dataset in CSV format into the Console app and ensure "Copy always" option is selected for the file in Copy to output directory in the file properties.
  
      <img width="267" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/62fa4d2e-c150-43ff-8d72-2ca7718324fd">

-  In the Console App , Add a class to represent the NYC Green Taxi dataset , we are adding the override string ToString() method to return Json object when ToString() is called , this is one way to ensure Json formatted data is sent to eventhub. 
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
  
- In the Console App , Modify the Program.cs file as below to send events to eventhub
  
       
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

- Login into Fabric portal [here](https://app.fabric.microsoft.com/) and choose Data Activator persona , This assumes you have signed for the private preview of Data Activator

  <img width="259" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d590d94f-452d-402d-9166-2fc2144579b8">
  
- Create a Reflex item
  
  <img width="452" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f10fb1f0-f611-4163-86fa-8ae21f6cd9ca">

- Click on Get Data and enter a name , Event hub connection string and leave Consumer group as $Default and Time field as empty 

   <img width="979" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a4adc9b0-03d1-456d-a2ba-7cc841e4ba20">
   
    <img width="454" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/e684a67a-5962-43e3-89ff-2ff0dd8aa0b7">

- The data would be loaded from the event hubs

   <img width="919" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d0323cd9-8fc4-4387-8457-6f813cec02bf">

   <img width="1093" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/afc90d47-5593-4457-8252-ec9c65577ad8">




   


