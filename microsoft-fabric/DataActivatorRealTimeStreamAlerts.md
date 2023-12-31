# Microsoft Fabric: No-Code Alerting on Real-time Streams using Data Activator

Data Activator is a no-code tool part of Microsoft Fabric that allows users to automatically take actions based on conditions and patterns in data. It monitors data in Power BI reports and Event Hub data streams, triggering actions such as email alerts. It enables businesses to build a alerting system that can handle data on a scale. This tool eliminates the need for manual monitoring and offers a no-code solution for acting on data insights.

Refer [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md) and [here](https://blog.fabric.microsoft.com/en-us/blog/driving-actions-from-your-data-with-data-activator/) for more info.

## High-level Architecture
<img width="734" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/c51da9f3-f35d-40b4-8249-9620ac5b1088">


## Things to Note 
- Microsoft Fabric and Data Activator are in preview, Please read [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md) and [here](https://blog.fabric.microsoft.com/en-us/blog/driving-actions-from-your-data-with-data-activator/) for complete info including FAQ's , Prerequisites , performance guidelines 
  
- The tutorial uses NYC Green Taxi Dataset, Available [here](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page) in Parquet format and CSV format dataset can be found Kaggle , The tutorial uses CSV format , Please convert Parquet to CSV format or use csv format dataset from Kaggle
  
- The tutorial uses a .NET Console app that mimics taxi Streaming device by sending few rows from NYC Green Taxi Dataset at preset intervals
  
- Please note that this tutorial is intended for explorative and illustrative purposes only. It is meant to inspire ideas and should not be taken as prescriptive advice. Any implementation of the techniques described in this tutorial as part of your application should be thoroughly validated and tested to ensure accuracy, validity, compatibility with your specific use case and technical environment.

- Data Activator can have Power BI report as source, we focus here on Azure EventHub real time streaming alerts in this article

## Prerequisites

- Azure subscription with Azure Event Hub resource. 
- Microsoft Fabric You can enable free trial [here](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial).
- Data Activator is in preview, you can request access [here](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbRzMF5NSZnI1CrCTbAYcvUW9UM1NWWUUzVlpEVVZJS0lSM1ZWUDBSTkMyVi4u)
- Refer [here](https://github.com/microsoft/dataActivator/blob/main/ConsolidatedDocs.md#faq-things-to-know-before-using-data-activator) for things to know before using Data Activator
- Visual Studio

## Technical Flow

- A sample .NET console app reads subset of NYC Taxi Dataset at pre-set intervals and sends events to Azure Event Hubs
  
- Data Activator gets data from Azure Event Hub, creates trigger based on data patterns and sends email alerts when data patterns are matched
  
  - Using visual options, when payment type of the streamed taxi data is of type "dispute" emails are sent

## Steps 

**Create an event hub using Azure portal**

- Follow the steps listed [here](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-create) to create a Azure Event Hub Resource from Azure Portal
  
- For simplicity , We will use the connection string of event hub resource for authentication in this sample , Please consider using Passwordless (Azure Active Directory authentication) for more secure applications . Refer [here](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-get-connection-string?source=recommendations#connection-string-for-a-specific-event-hub-in-a-namespace) to get the connection string for the event hub resource

**Create an .NET Console App to send data to EventHub**

*Please note this sample console app tries to mimic a streaming app by just sending few random rows to EventHub at pre-configured intervals, the code does not handle errors , nor use production grade authentication and does not guarantee uniqueness of records being sent, this is created just for demo purposes only*

- Follow the steps listed [here](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send?tabs=connection-string%2Croles-azure-portal) to setup the C# console app to send the events to EventHub created as part of above step, we will use the connection string option for event hub authentication
  
- Please note, we would be using the .NET app to send events and would not be receiving events from event hub , so please exclude Receive events from the event hub section in the above link

  <img width="413" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/3eb8d1fe-b13a-44b0-aa5e-cbba8e9d6f39">

- For more details, you could watch "AZ-204 - Connect and Consume Services - Azure Event Hubs" YouTube video by Alan Rodrigues  [here](https://www.youtube.com/watch?v=HwZldR8KlKM)

-  Copy the NYC Green Taxi dataset in CSV format into the Console app and ensure "Copy always" option is selected for the file in Copy to output directory in the file properties.
  
      <img width="267" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/62fa4d2e-c150-43ff-8d72-2ca7718324fd">

- In the Console App, Add a class to represent the NYC Green Taxi dataset , we are adding the override string ToString() method to return Json object when ToString() is called , this is one way to ensure Json formatted data is sent to EventHub. 
     
      
      using System.Text.Json;
      
      
      namespace SendToAzureEventHub
      {
          internal class NycGreenTaxiDataFromFile
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
      
      
          
      
          }
      
          internal class NycGreenTaxiData
          {
              public int VendorID { get; set; }
              public DateTime Lpep_pickup_datetime { get; set; }
              public DateTime Lpep_dropoff_datetime { get; set; }
              public string Store_and_fwd_flag { get; set; }
              public string RatecodeID { get; set; }
      
              public string PULocationID { get; set; }
      
              public string Pickup_borough { get; set; }
      
              public string Pickup_zone { get; set; }
      
              public string Pickup_service_zone { get; set; }
      
              public string DOLocationID { get; set; }
      
              public string Dropoff_borough { get; set; }
      
              public string Dropoff_zone { get; set; }
      
              public string Dropoff_service_zone { get; set; }
      
              public string Passenger_count { get; set; }
      
              public double Trip_distance { get; set; }
      
              public double Direct_distance { get; set; }
      
      
              public double Fare_amount { get; set; }
      
              public double Extra { get; set; }
      
              public double MTA_tax { get; set; }
      
              public double Tip_amount { get; set; }
      
              public double Tolls_amount { get; set; }
      
              public string eHail_fee { get; set; }
      
              public double Improvement_surcharge { get; set; }
      
              public double Total_amount { get; set; }
      
              public string Payment_type { get; set; }
      
      
              public string Trip_type { get; set; }
      
      
              public string Congestion_surcharge { get; set; }
      
              public string? UniqueID { get; set; }
      
              public override string ToString()
              {
                  return JsonSerializer.Serialize(this);
              }
      
          }
      }



- Add the CsvHelper package via Nuget, Refer [here](https://www.nuget.org/packages/CsvHelper/)
  
- In the Console App, Modify the Program.cs file as below to send events to EventHub
  
       
        using Azure.Messaging.EventHubs.Producer;
        using CsvHelper;
        using System.Globalization;
        
        
        namespace SendToAzureEventHub
        {
            internal class Program
            {
                //created two objects one for file and one for having uniqueid for data activator
                public static List<NycGreenTaxiDataFromFile> taxiDataLst = new List<NycGreenTaxiDataFromFile>();
                public static List<NycGreenTaxiData> taxiDataLstOut = new List<NycGreenTaxiData>();
                public static List<NycGreenTaxiData> sendToHub = new List<NycGreenTaxiData>();
                static void Main(string[] args)
                {
                    //read data from csv file
                    PrepStream();
                    //make 10 calls if left running
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
                    //return random index between zero and 50 records less than total rows in dataset ,
                    //no check for exceptions done
                    int minnum = random.Next(0, taxiDataLstOut.Count() - 50);
                    //get 50 random rows to be sent to eventhub based on index , doesnot do duplicate checks reads 50 rows at random ,
                    //no check for exceptions done
                    sendToHub = taxiDataLstOut.GetRange(minnum, 50);
        
        
                   
                    //connection string to eventhib
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
                        taxiDataLst = csv.GetRecords<NycGreenTaxiDataFromFile>().ToList();
                    }
                    //taxiDataLst = taxiDataLst.Where(r => r.payment_type== "4").ToList();
                    foreach (var taxi in taxiDataLst)
                    {
                        taxiDataLstOut.Add(new NycGreenTaxiData
                        {
                            Congestion_surcharge = taxi.congestion_surcharge,
                            Direct_distance = taxi.direct_distance,
                            DOLocationID = taxi.DOLocationID,
                            Dropoff_borough = taxi.dropoff_borough,
                            Dropoff_service_zone = taxi.dropoff_service_zone,
                            Dropoff_zone = taxi.dropoff_zone
                            ,
                            eHail_fee = taxi.ehail_fee,
                            Extra = taxi.extra,
                            Fare_amount = taxi.fare_amount,
                            Improvement_surcharge = taxi.improvement_surcharge
                            ,
                            Lpep_dropoff_datetime = taxi.lpep_dropoff_datetime,
                            Lpep_pickup_datetime = taxi.lpep_pickup_datetime,
                            MTA_tax = taxi.mta_tax,
                            Passenger_count = taxi.passenger_count,
                            Payment_type = taxi.payment_type,
                            Pickup_borough = taxi.pickup_borough,
                            Pickup_service_zone = taxi.pickup_service_zone,
                            Pickup_zone = taxi.pickup_zone,
                            PULocationID = taxi.PULocationID,
                            RatecodeID = taxi.RatecodeID,
                            Store_and_fwd_flag = taxi.store_and_fwd_flag,
                            Tip_amount = taxi.tip_amount,
                            Tolls_amount = taxi.tolls_amount,
                            Total_amount = taxi.total_amount,
                            Trip_distance = taxi.trip_distance,
                            Trip_type = taxi.trip_type,
                            UniqueID = Guid.NewGuid().ToString(),
                            VendorID = taxi.VendorID
        
                        }
        
                           );
                    }
        
                }
            }
        }
- Add your event hub connection string in EventHubProducerClient _client = new EventHubProducerClient("your eventhub connection string here"); section
  
- Build the solution and run the console app, close the app after confirming the events are sent , please note the app sends 50 events every 6 seconds and does 10 loops , stop after seeing events sent message

  <img width="860" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8424e2df-874e-4727-9d65-c66d48b8143c">

**Create No-code Data Activator to send alerts from Microsoft Fabric**

- Login into Fabric portal [here](https://app.fabric.microsoft.com/) and choose Data Activator workload, This assumes you have signed up for the private preview of Data Activator

  <img width="259" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d590d94f-452d-402d-9166-2fc2144579b8">
  
- Create a Reflex item
  
  <img width="452" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f10fb1f0-f611-4163-86fa-8ae21f6cd9ca">
   
    <img width="454" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/e684a67a-5962-43e3-89ff-2ff0dd8aa0b7">

- The data would be loaded from the event hubs

   <img width="919" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d0323cd9-8fc4-4387-8457-6f813cec02bf">

    <img width="753" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/bb72d8d2-3b02-4cf2-980e-f12fc495441c">


- We will need at least one of the keys in the event hub Json data to uniquely identify the data row. This can be something like device id, package id or taxi meter id , we have   UniqueID  property that identifies unique event through Guid

- we have created a unique id in our console app that mimics streaming 

- Click on three dots on the UniqueID and add create an object and name the object 

   <img width="365" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/47d4e514-928f-4420-94cc-5b486171abcf">

    <img width="239" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/002fe7f3-853c-4f8c-8afd-3b74ac22be16">



- Creating the object would open up the design tab with 5 UniqueID’s selected as reference and available in the graph, The following image shows you the events that were received split out by the unique values from the UniqueID column.

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

- Enter the value as 4 in the value field , the value of 4 is represented in the graph , if you do not see value in the graph then chose the UniqueID which has payment_type of 4 by looking up the value from data tab, when selecting the data tab , save the progress 

  <img width="889" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/59c4a7e3-11ec-4ddc-ab65-60cf73047ff4">

  <img width="631" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f00da54c-dcfa-433c-b412-c194ff7bf70a">
  
   <img width="917" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/4cc368d3-c3af-4f79-a03b-091d149e7e2e">

    <img width="716" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/5b483c00-0bdb-45b6-86af-ff01e0db7032">

    <img width="961" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/9dc9bb9d-a059-4d4f-813b-ce2ceb0c7009">



 - If you don’t see value of 4 in your data, we could stream more data from the console app or use a available payment_type or other value for alerting

 - In this detect mode, we are tracking at every event the has payment_type as dispute. Lets change the every time option to Number of times and establish the window of time , for example detect when Payment_type of 4 (dispute) happens 5 times over 1 minute 

   <img width="929" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/6f8c4e77-d3db-4fe4-9668-e2f4bae34653">

 
 - In the next step click on Act and send email to create a trigger

   <img width="913" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f93ee1b6-0c3d-4738-8cc0-e8cde9cb9bdb">
   
 - From the send email section, we can click on "Send email to me" to send a test notification 

   <img width="916" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/dbe0ad1d-65d9-43f5-a38b-a9f2d1ed04d8">

   <img width="790" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a09c3d6f-74ab-4292-86ad-ea123995275d">

  
 - Below is the one sample email recieved

    <img width="974" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/379ad72b-f3f8-4f96-b77f-04291900d98d">



- We could update subject , headline , optional message to  better match the alerting conditon  

   <img width="904" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/4ab246c9-c5b7-45d8-8837-c12563063d25">

- Now to publish the draft trigger click on start and click on continue to publish and have the trigger running

  <img width="776" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/04f7fb1f-0fac-4a05-ae99-12ac695e9269">


- We could stream the data using console app and whenever the condition of when Payment_type of 4 (dispute) happens 5 times over 1 minute the emails would be sent. Please ensure set the alert criteria  higher (like 100 times over 1 minute) to prevent inbox being flooded with alert emails when condition is met , Stop the trigger when done
  
    <img width="664" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/af3056b3-2ac8-43c1-8b20-edf65d043c45">

