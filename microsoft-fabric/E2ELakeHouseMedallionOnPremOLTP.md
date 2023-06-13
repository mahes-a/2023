
# Microsoft Fabric : Build end-to-end Medallion Lakehouse architecture  from On-Premise OLTP Source

## Architecture


![image](https://github.com/mahes-a/2023/assets/120069348/dfa357df-5eae-4769-97ea-1cfd585e820a)



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
  
  - 4 Simplified tables based on  Wide World Importers forms the dataset to be ingested , dataset can be found [here](https://learn.microsoft.com/en-us/sql/samples/wide-world-importers-what-is?view=sql-server-ver16)
    
  - The tables are normalized and have parent child relationship , For example we could chose below tables from wide world importers dataset
  
      <img width="839" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f025f7be-f875-4905-9fa2-5f1a84b7092c">

  
  - If you have not setup the On-premises data gateway to establish connection between the on-prem SQL and Power BI (Fabric) service. Please do by following instructions  [here](https://learn.microsoft.com/en-us/data-integration/gateway/service-gateway-install)

#### Create Lakehouse 

  - Browse to your Fabric enabled workspace in Power Bi and switch to Data Engineering persona
      
      <img width="71" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/2151ab70-9876-48d7-83b9-42a15490021c">
      
      
      
  - Click on New Lakehouse and name your lakehouse 
      <img width="713" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/dc56548d-f030-4200-b150-8b313ea920d9">
      
      <img width="254" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ab4a450d-320a-4de1-9f11-f819dea4c2e8">

#### Create Dataflow and ingest data into bronze layer

 - Select New Dataflow Gen 2 to open the Dataflow screen
 
      <img width="646" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/04911b7f-4f2f-4ab3-85ec-bc2efc3b9dd5">
      
      
  - Select the Import from SQL server in the dataflow
  
      <img width="1135" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/315b1502-58e8-4988-80ff-0c710a3d8991">
      
  - Provide the SQL server details and the Gateway name to establish the connection 
  
  
       <img width="525" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/fee9c03c-8c5a-4fd7-9277-1d01bf863c75">
    
   - In the choose data window Select the tables to ingested and click on create    
   
       <img width="1127" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/fa08c9fe-05f8-402a-8dde-2b66f1ed59e5">




   - In the Power Query window , select the destination as lakehouse (bottom right corner) . We are not doing any transformations in the dataflow and loading directly to Lakehouse 
   
   
        <img width="143" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/729155de-d1d8-43df-a0f4-9c461459f04d">
  
   - Choose the lakehouse and select next and choose your lakehouse created in previous step and provide a new table name  , save the destination settings and mapping , Repeat for all the tables selected and publish the          dataflow

        
        <img width="598" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/7795db7f-370a-4c4b-b399-840166fdbea9">
        
        <img width="515" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/fba1df49-d115-4c69-92c8-89a003edd285">
      
        <img width="392" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a1b7ade2-6c02-4b4d-9857-a08f1005ee01">



   - Wait for the dataflow to complete and open the lakehouse and validate the tables are created under the tables section and validate the data 

        <img width="207" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/ee3aa803-7907-4b35-ab5d-08129383319b">
        
        
        <img width="616" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/fab62f63-2302-4afc-9f4e-ee6bece88fab">

   
   
   - These will be our bronze layer tables ingested from source.  
   
   - For curating the silver layer , Notebooks can be used
   
      <img width="646" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/00fd54fb-36ac-43fc-9842-200fc61bf246">
      
  #### Create Silver layer using notebooks
      
   - While the bronze layer contains the entire data history in a nearly raw state, the silver layer represents a validated, enriched ,cleaned version of our data that can be trusted for downstream analytics.
   
   -  In the notebook , we can enable Vorder and Optimize write and file sizes using below spark configs
        <img width="581" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/b8ede13d-a5b8-4e37-a3ee-c77ddad44213">

         
   - As an example in the sliver layer State and City raw tables can be joined and invoice and invoicelines can be joined. select only columns that is needed for anlaytics purposes from bronze layer and create sliver  datasets. Run the notebook and validate the data from lakehouse explorer
   
       <img width="890" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a14ca230-c6df-46f3-8685-3b28b2b3961a">

       
       <img width="520" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8032593f-5b58-45e5-828c-9af543ab0c13">


 #### Create Gold layer using notebooks
   
   - Data in the Gold layer of the lakehouse is typically organized in consumption-ready structures with a data model like star schema applied. Data is aggregated based on business and reporting needs ready to be served 
   
   - In our sample lets create a sample star schema model in gold layer by creating a Dimension city table and Fact Sales table from Silver layer. As part of gold layer create a surrogate key for city dimension and add the surrogate key to fact table using a notebook 
      <img width="902" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/3dd18203-7662-4a81-b4e6-a2f90aa053b9">

   -  Validate the gold layer data in the lakehouse explorer 

      <img width="559" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/eaad1706-f344-47b0-9585-161f5e60670e">
 
 #### Create data model for reporting using SQL endpoint
  
   - In the Lakehouse explorer switch to Sql end point. Select the model tab from left bottom 
        
       <img width="290" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/384a3c07-0b3b-4e8f-8abc-8707b3b2a99d">


   
   - For this data model, you need to define the relationship between different tables so that you can create reports and visualizations based on data coming across different tables. From the fact_sales table, drag the CityKey field and drop it on the CityKey field in the dim_city table to create a relationship. Check the “Assume referential integrity” and select Confirm to establish the relationship. Note - When defining relationships, make sure you have many to one relationship from the fact to the dimension and not vice versa
   
   <img width="436" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/9812d78f-1427-445e-b6f4-42a175fbf07c">

   
   - On the Create Relationship settings:

    Table 1 is populated with fact_sales and the column of CityKey.

    Table 2 is populated with dim_city and the column of CityKey.

    Cardinality: Many to one (*:1)

    Cross filter direction: Single

    Leave the box next to Make this relationship active checked.

    Check the box next to Assume referential integrity.

    Select Confirm.
    

<img width="541" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/f11d6bab-93c9-4b43-b138-bf45001694f6">

#### Create data set and power bi report
  
- On the reporting tab select "New Power BI dataset" , provide dataset name and select the fact and dimension table

  <img width="898" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8a4c13bc-69dc-4e6a-8235-f9f5a9138646">
  
  
  
  <img width="427" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/a0989511-a3d5-450b-91b5-4a6e79ed3546">

  

- Rename the dataset to your liking 


   <img width="419" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/cc6169c9-9e0a-4a91-bf56-3c104aa9be00">

- Go to your workspace and open the datset created and select Auto create report and pre- select the dimesnion and fact fields to create the report. Save the report 
  
  <img width="1116" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8beb9c0c-7d90-4a40-aa1e-117be7435f55">
  
  
  
  <img width="805" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/d4843d68-3f1b-491f-ae5b-66ba4ab907c1">
  
  
  <img width="1133" alt="image" src="https://github.com/mahes-a/2023/assets/120069348/8eca6838-acb1-4eba-9cef-9dbe69f75215">



   
     
     



   
   

        

   
      



   
      



        
    
        
      
       

      
        








