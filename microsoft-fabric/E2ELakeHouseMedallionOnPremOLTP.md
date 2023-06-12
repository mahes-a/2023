
# Microsoft Fabric : Build end-to-end Medallion Lakehouse architecture  from On-Premise OLTP Source


## Prerequisites

- Microsoft Fabric You can enable free trial [here](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial). 
- Microsoft SQL Server (On-Premise)
- On-premises data gateway 

## Flow

- Create Microsoft Fabric Lakehouse
- Use Dataflow Gen2 to ingest data from SQL source into Lakehouse bronze layer
- Use Notebooks to curate Silver and Gold layer datasets
- Data Model the relationships between the Gold layer datasets 
- Use Power BI to consume the data 
  
 ## Steps
  
  #### Choose the OLTP Data Set for ingestion
  
  - 4 Simplified tables based on  Wide World Importers forms the dataset to be ingested , databaset can be found [here](https://learn.microsoft.com/en-us/sql/samples/wide-world-importers-what-is?view=sql-server-ver16)

    <img width="872" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/51d5b465-cf33-4064-bc2e-acf2533518fb">
    
  - The tables are normalized 
  
  - If you have not setup the On-premises data gateway to establish connection between the Power BI (Fabric) service. Please do by following instructions  [here](https://learn.microsoft.com/en-us/data-integration/gateway/service-gateway-install)
  
  - Browse to your Fabric enabled workspace in Power Bi and switch to Data Engineering persona
      
      <img width="71" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/2151ab70-9876-48d7-83b9-42a15490021c">
      
      
      
  - Click on New Lakehouse and name your lakehouse 
      <img width="713" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/dc56548d-f030-4200-b150-8b313ea920d9">
      
      <img width="254" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ab4a450d-320a-4de1-9f11-f819dea4c2e8">


 - Select New Dataflow Gen 2 to open the Dataflow screen
 
      <img width="646" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/04911b7f-4f2f-4ab3-85ec-bc2efc3b9dd5">
      
      
  - Select the Import from SQL server in the dataflow
  
      <img width="1135" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/315b1502-58e8-4988-80ff-0c710a3d8991">
      
  - Provide the SQL server details and the Gateway name to establish the connection 
  
  
       <img width="525" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/fee9c03c-8c5a-4fd7-9277-1d01bf863c75">
    
   - In the choose data window Select the tables to ingested and click on create    

   - In the Power Query window , select the destination as lakehouse (bottom right corner) . We are not doing any transformations in the dataflow and loading directly to Lakehouse 
        <img width="143" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/729155de-d1d8-43df-a0f4-9c461459f04d">
  
   - Choose the lakehouse and select next and choose your lakehouse created in previous step and provide a new table name , save the destination settings and mapping , Repeat for all the tables selected and publish the          dataflow

        
        <img width="598" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/7795db7f-370a-4c4b-b399-840166fdbea9">
        
        <img width="515" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/fba1df49-d115-4c69-92c8-89a003edd285">
      
        <img width="392" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a1b7ade2-6c02-4b4d-9857-a08f1005ee01">




   - Wait for the dataflow to complete and open the lakehouse and validate the tables are created under the tables section and validate the data 

        <img width="207" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ee3aa803-7907-4b35-ab5d-08129383319b">
   
   
   
   - These will be our bronze layer tables ingested from source .  
   
      



        
    
        
      
       

      
        








