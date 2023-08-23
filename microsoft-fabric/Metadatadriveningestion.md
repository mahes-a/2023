# Microsoft Fabric Scalable and Efficient OneLake Hydration: Bronze Layer Curation for Full, Change Tracking, CDC, and Watermark Change Capture Mechanisms  #

In today's data-driven world, organizations need efficient and adaptable data pipelines to process and derive insights from vast amounts of information. One powerful approach is to leverage change tracking, CDC, and watermark change capture mechanisms. These mechanisms enable incremental data loading, real-time or near-real-time data availability, and improved scalability. In this blog, we explore the steps of implementing a generic data pipeline that incorporates these mechanisms and how it can revolutionize your data processing workflows in Fabric

Benefits of a Generic Pipeline:

-  Enhanced Efficiency: By capturing and processing only the changes made to the data, the pipeline reduces processing time and resource consumption, resulting in faster data loading and improved performance.

- Near Real-time Data Availability: Change tracking, CDC, and watermark change capture mechanisms ensure that the most recent data is readily available for analysis and decision-making, empowering organizations to make timely and informed choices.
  
- Incremental Data Loading: The pipeline processes only the newly added or modified data, eliminating the need to reload the entire dataset. This reduces processing time and minimizes the impact on system resources.
  
- Data Accuracy and Consistency: Capturing changes at the source level ensures data integrity and consistency throughout the pipeline, avoiding inconsistencies that may arise from manual or batch-based data updates.
  
- Flexibility and Adaptability: A generic pipeline can seamlessly handle different data sources and formats, allowing for easy integration of new sources or modifications without significant changes to the pipeline architecture.
  
- Scalability: By efficiently handling large volumes of data and increasing data velocity, the pipeline can scale effortlessly, ensuring smooth operations and minimal disruptions.


## High-level Architecture

 <img width="736" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/68d6b525-6eaa-4989-bfa2-d50ecae9dcf7">


## High-level Flow

The High level flow  involves the following steps:

- The SQL Tables to be copied and their Full/Incremental copy methodology (Full , Change Capture , CDC , Watermark) are configured in a control table

- Generic Pipelines are created that can ingest incremental data based on the change capture mechanism
  
   -  Pipelines that can copy data for Full load , incremental data copy based on Change Capture , CDC , Watermark are created. 

- Generic Pyspark notebooks that can Upsert and Delete for each incremental data copy mechanism (Change Capture , CDC , Watermark) are created.

- Orchestration pipeline is designed to execute the ingestion and curation notebook for each incremental data copy mechanism (Change Capture , CDC , Watermark)

- Orchestration reads from the control table and for each table to be ingested the corresponding ingestion pipeline and curation notebook are executed.

  -  For example , If the table to be ingested has CDC has the change capture mechanism then CDC Ingestion and Bronze layer curation notebook is executed
  
- The Orchestration pipeline executes the ingestion and curation in a parallel fashion for each table to ingested


     ![image](https://github.com/mahes-a/StagingBuild/assets/120069348/ffa93127-f096-4dfc-9b83-c9bedd3bf396)


## Prerequisites


- Microsoft Fabric You can enable free trial [here](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial).
- Access to Azure Family of  SQL Databases


## Things to Note 

- This Article assumes the Lakehouse and source tables exist and connections are established. In order to know more about how to create End to End Analytics in Fabric Lakehouse refer [here](https://github.com/mahes-a/2023/blob/main/microsoft-fabric/E2ELakeHouseMedallionOnPremOLTP.md)

-  Refer [here](https://learn.microsoft.com/en-us/azure/data-factory/copy-data-tool-metadata-driven) to learn more on Metadata driven pipelines

- To understand more about the various change capture mechanisms and the logic to ingest the incremental rows can be found [here](https://learn.microsoft.com/en-us/azure/data-factory/tutorial-incremental-copy-change-data-capture-feature-portal) 
  
- Re-runs after failure and explicit exception handling are not added as part of this tutorial

- Inspired and Adapted from [Incremental Data Copy Tutorial](https://learn.microsoft.com/en-us/azure/data-factory/tutorial-incremental-copy-overview)

- Please note that this tutorial is intended for explorative and illustrative purposes only. It is meant to inspire ideas and should not be taken as prescriptive advice. Any implementation of the techniques described in this tutorial as part of your application should be thoroughly validated and evaluated to ensure accuracy, validity, compatibility with your specific use case and technical environment.

## Steps


**MetaData SQL Configurations**

-  Create the Artifacts required for the Metadata driven ingestion by following below steps
   
- Below is a sample schema for the configuration table to store the tables to be ingested

   
         CREATE TABLE [dbo].[ControlTable](
         	[TableID] [int] IDENTITY(1,1) NOT NULL,
         	[SourceTableName] [varchar](255) NULL,
         	[FolderPath] [varchar](800) NULL,
         	[SchemaName] [varchar](50) NULL,
         	[PartitionColumnName] [varchar](255) NULL,
         	[PartitionLowerBound] [varchar](50) NULL,
         	[PartitionUpperBound] [varchar](50) NULL,
         	[LakeHouseName] [varchar](255) NULL,
         	[OneLakePath_or_TableName] [varchar](255) NULL,
         	[CopyMode] [varchar](50) NULL,
         	[DeltaColumnName] [varchar](255) NULL,
         	[IsActive] [varchar](10) NULL,
         	[WaterMarkColumnName] [varchar](100) NULL,
         	[ProcessingPath] [varchar](255) NULL,
         	[LakehouseArtifactID] [varchar](255) NULL
         ) ON [PRIMARY]
         GO
- Below is a sample schema for the log table to store custom pipleine run data

          CREATE TABLE [dbo].[IngestionLog](
        	[Id] [bigint] IDENTITY(1,1) NOT NULL,
        	[ServerName] [varchar](100) NULL,
        	[DbName] [varchar](100) NULL,
        	[TableName] [varchar](100) NULL,
        	[RunStatus] [varchar](50) NULL,
        	[SourceCount] [bigint] NULL,
        	[BronzeCount] [bigint] NULL,
        	[UpdateDate] [datetime] NULL,
        	[ErrorMessage] [varchar](max) NULL,
         CONSTRAINT [PK_IngestionLog] PRIMARY KEY CLUSTERED 
        (
        	[Id] ASC
        )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
        ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
        GO

-  Below is a sample schema for storing the last changed version for Watermark , CDC , Change Tracking
  
        CREATE TABLE [dbo].[table_store_watermark_value](
        	[ServerName] [varchar](255) NULL,
        	[TableName] [varchar](255) NULL,
        	[WatermarkValue] [datetime] NULL
        ) ON [PRIMARY]
        GO

        CREATE TABLE [dbo].[table_store_ChangeTracking_version](
        	[TableName] [varchar](255) NULL,
        	[SYS_CHANGE_VERSION] [bigint] NULL
        ) ON [PRIMARY]
        GO
        
        CREATE TABLE [dbo].[table_store_CDC_version](
        	[ServerName] [varchar](255) NULL,
        	[TableName] [varchar](255) NULL,
        	[CDCLastRun] [varchar](255) NULL
        ) ON [PRIMARY]
        GO

-  Below are stored procedures that can upsert in to above mentioned tables

      
       CREATE PROCEDURE [dbo].[InsertLog]
       (
           -- Add the parameters for the stored procedure here
            @ServerName   varchar (100) NULL,
       	 @DbName   varchar (100) NULL,
       	 @TableName   varchar (100) NULL,
       	 @RunStatus   varchar (50) NULL,
       	 @SourceCount   bigint  NULL,
       	 @BronzeCount   bigint  NULL,
       	 @error varchar(max)
       )
       AS
       BEGIN
           -- SET NOCOUNT ON added to prevent extra result sets from
           -- interfering with SELECT statements.
           SET NOCOUNT ON
       
       
       
       INSERT INTO  dbo.IngestionLog 
                  ( ServerName 
                  , DbName 
                  , TableName 
                  , RunStatus 
                  , SourceCount 
                  , BronzeCount 
       		   ,ErrorMessage
                  , UpdateDate )
            VALUES
                  (@ServerName,  
                  @DbName,  
                  @TableName,  
                  @RunStatus,
                  @SourceCount,
                  @BronzeCount, 
       		   @error,
                  GETDATE())
       
       
       
       
       END
       
     
       SET ANSI_NULLS ON
       GO
       SET QUOTED_IDENTIFIER ON
       GO
       
       CREATE PROCEDURE [dbo].[Update_CDCLSN_Version]  @serverName varchar(255), @TableName varchar(255), @lsn varchar(50)
       AS
       BEGIN
       
       IF EXISTS(SELECT 1 FROM dbo.table_store_CDC_version WHERE ServerName = @serverName and TableName=@TableName)
            UPDATE dbo.table_store_CDC_version 
            set [CDCLastRun] = @lsn
       	 where ServerName = @serverName and TableName=@TableName
       ELSE
            INSERT INTO dbo.table_store_CDC_version
                 (ServerName, TableName, CDCLastRun)
            VALUES
                 (@serverName,@TableName,@lsn)
       
       
       END
       
     
       SET ANSI_NULLS ON
       GO
       SET QUOTED_IDENTIFIER ON
       GO
       CREATE PROCEDURE [dbo].[Update_ChangeTracking_Version] @CurrentTrackingVersion BIGINT, @TableName varchar(50)
       AS
       BEGIN
       UPDATE table_store_ChangeTracking_version
       SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
       WHERE [TableName] = @TableName
       END
       GO
      
       SET ANSI_NULLS ON
       GO
       SET QUOTED_IDENTIFIER ON
       GO
       
       
       CREATE PROCEDURE [dbo].[Update_WaterMark_Value]  @serverName varchar(255), @TableName varchar(255), @watermark datetime
       AS
       BEGIN
       
       IF EXISTS(SELECT 1 FROM dbo.table_store_watermark_value WHERE ServerName = @serverName and TableName=@TableName)
            UPDATE dbo.table_store_watermark_value 
            set [WatermarkValue] = @watermark
       	 where ServerName = @serverName and TableName=@TableName
       ELSE
            INSERT INTO dbo.table_store_watermark_value
                 (ServerName, TableName, WatermarkValue)
            VALUES
                 (@serverName,@TableName,@watermark)
       
       
       END
       
       GO


##### Configure the tables to be ingested 

- Insert the tables to be ingested in the control table
  
- The Copy Mode indicates the nature of copy - CDC , CHANGE TRACKING , Full , WATERMARK are the values to be configured
  
- For Full copy mechanism where count of rows to be copied can be very high , we can enable parallel data copy using the Partition column names
  -  PartitionColumnName would denote the partition column that the pipeline can use to select in multiple threads
  -  PartitionLowerBound and PartitionUpperBound denote the upper and lower bound for the parallel copy 

- Refer [here](https://learn.microsoft.com/en-us/azure/data-factory/connector-sql-server?tabs=data-factory#parallel-copy-from-sql-database) to understand various parallel copy partition options  

- In order to configure the Target Lakehouse file location and the target lakehouse in fabric follow below steps
  
-  To retrieve the LakeHouseName , Select any table or file within the fabric lakehouse and click on properties and select the ABFS path until the lakehouse name , the format would be abfss://<workspacename>@<tenanttname>-onelake.dfs.fabric.microsoft.com/<lakehousename>.Lakehouse

      <img width="439" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/0248e8ff-efd7-4d68-8d7a-73968aff4132">

 -  One way to retreive the lakehouse artifact id to enable run time  Lakehouse sink creation , create a data pipeline and drag a copy activity and chose the lakehouse and view code the Json code would provide the artifactId for Lakehouse which needs to configured in LakehouseArtifactID column
   
 
  <img width="628" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/54d50fc5-d1ea-42ee-9db3-1cc89344ad6e">


  <img width="841" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/702b945c-2a30-4064-b443-f6a89a14ecb7">

 - The paths where the files to be landed for processing in one lake should be configured , these paths are relative and can be for example "/tobeprocessed/cdc/"

 - The Primary keys of the tables would be configured in deltacolumnname and for watermark additional watermark column name usually a date column is also needed

 - The control table would be as below after configuration

   <img width="1193" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/29b062d4-364c-498b-86d9-baae9bbc68fa">

##### Building Generic Ingestion Pipelines

#### Note:- The Idea to create individual pipelines to handle Full and other change capture mechanism is to address separation of concerns , flexibility in deployment and ease of maintenance 

##### Creating the Generic CDC Load Pipeline

- Browse to your Fabric enabled workspace in Power Bi and switch to Data Factory and create a new pipeline

  <img width="388" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/5d27b4f1-b4a0-4ff0-9483-f6babc7b0cf6">

- Name the Pipeline related to CDC , For example "PL_GENERIC_SQL_CDC_INGEST"
  
- The CDC workflow is as below
 -  Select the From LSN last processed and the current pipeline run date as To LSN
 -  Use the CDC function fn_cdc_get_all_changes_  or fn_cdc_get_net_changes_  to retrieve the delta between the previous run and current run
 -  Copy the delta data into Parquet files and make a transient copy to processing folder
 -  The copy of processing folder will be deleted after curation , this way you keep the actual data landed from Source SQL for logging , auditing , debugging needs
 -  Update the ingestion log and the CDC LSN table to store the latest successful LSN value for next run 
  
- Add the parameters required for the pipeline , all these parameters will be read from the control and the CDC version table

          "parameters": {
                   "TableName": {
                       "type": "string"
                   },
                   "SchemaName": {
                       "type": "string"
                   },
                   "LakeHouseName": {
                       "type": "string"
                   },
                   "OneLakePath": {
                       "type": "string"
                   },
                   "LSNStartTime": {
                       "type": "string"
                   },
                   "LSNEndTime": {
                       "type": "string"
                   },
                   "ProcessingPath": {
                       "type": "string"
                   },
                   "UniqueID": {
                       "type": "string"
                   }
        
- Create lookup activity to retrieve the last run LSN from LSN Table


  <img width="512" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/687b16bc-5bea-4ff7-ac97-cb42ef0fb4ef">

        Select * from dbo.table_store_CDC_version where TableName='@{concat(pipeline().parameters.SchemaName,'.', pipeline().parameters.TableName)}'


  - Using a Lookup activity to retrieve the count of changed rows between last run and current run

                 @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10) ; 
             SET @begin_time = ''',activity('LookupLSNCDC').output.firstRow.CDCLastRun,''';
             SET @end_time = ''',pipeline().parameters.LSNEndTime,''';
             SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
             SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than or equal'', @end_time);
             SELECT count(1) changecount FROM cdc.fn_cdc_get_net_changes_',pipeline().parameters.SchemaName,'_',pipeline().parameters.TableName,'(@from_lsn, @to_lsn,   ''all'')')
  
- Add an if condition and only if there are count of records to ingest then we will have to copy the records


  <img width="781" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/9bc65640-b1a5-4813-b7ba-13e16b502572">


        @greater(int(activity('Get CDC Change Count').output.firstRow.changecount),0)


- Within the True section of the If activity , Copying of the delta data and updating the logs and LSN values happen

  <img width="784" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/c51ba082-710b-404a-8706-371c91258f37">

- In the copy activity source , establish connection to SQL server and use below query to pull incremental  data from the CDC functions
  
 <img width="751" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/c32cd554-1be6-4e16-902a-35ccad3e59f9">

          @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10) ; 
         SET @begin_time = ''',activity('LookupLSNCDC').output.firstRow.CDCLastRun,''';
         SET @end_time = ''',pipeline().parameters.LSNEndTime,''';
         SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
         SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than or equal'', @end_time);
         SELECT * FROM cdc.fn_cdc_get_all_changes_',pipeline().parameters.SchemaName,'_',pipeline().parameters.TableName,'(@from_lsn, @to_lsn, ''all'')')


- In the destination of the Copy Activity Parameterize the LakeHouse object ID and the file path to be written

    <img width="800" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/de7bb4b0-851e-4ea5-8352-6e6dd29a8e81">

              @pipeline().parameters.LakeHouseName
                @concat(pipeline().parameters.OneLakePath,pipeline().parameters.TableName,'/','year=',formatDateTime(utcnow(),'yyyy'),'/','month=',formatDateTime(utcnow(),'MM'),'/','day=',formatDateTime(utcnow(),'dd'),'/',pipeline().parameters.UniqueID)
          

- Update the LSN value for next run using the Stored procedure 
   <img width="1074" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/34735673-b387-49b4-bc9a-c3d69160fac5">

- Insert the  log tables with Success status and the counts copied by executing the Stored Proc

     <img width="866" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/f5f763b8-59c2-42cc-9efc-8f8ff792ad50">

- In case of failure , Insert the  log tables with Failure status and the counts copied by executing the Stored Proc

    <img width="748" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/2f21d635-d0d4-4d64-b6bb-8fd0e14c686f">

- Copy the incremental files to a processing folder to be curated by notebooks in next step , this is a optional step and the bronze tables can be curated directly from the files copied in the earlier activity ,  this way you keep the actual data landed from Source SQL for logging , auditing , debugging needs and curation can be done as a separate process


  <img width="887" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/0605952c-2ef5-43da-8a24-db9968801086">

  Source and destination file path configurations are as below

            @concat(pipeline().parameters.OneLakePath,pipeline().parameters.TableName,'/','year=',formatDateTime(utcnow(),'yyyy'),'/','month=',formatDateTime(utcnow(),'MM'),'/','day=',formatDateTime(utcnow(),'dd'),'/',pipeline().parameters.UniqueID)

          @concat(pipeline().parameters.ProcessingPath,pipeline().parameters.TableName)

##### Creating the Generic Change Tracking Load Pipeline

- Browse to your Fabric enabled workspace in Power Bi and switch to Data Factory and create a new pipeline

  <img width="388" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/5d27b4f1-b4a0-4ff0-9483-f6babc7b0cf6">

- Name the Pipeline related to Change Tracking , For example "PL_Generic_SQL_CHANGETRACKING"

- For understanding the flow of the pipeline refer [here](https://learn.microsoft.com/en-us/azure/data-factory/tutorial-incremental-copy-overview#delta-data-loading-from-sql-db-by-using-the-change-tracking-technology)

- Define the parameters for the pipeline
          
             "parameters": {
                      "TableName": {
                          "type": "string"
                      },
                      "SchemaName": {
                          "type": "string"
                      },
                      "DeltaColumnName": {
                          "type": "string"
                      },
                      "LakeHouseName": {
                          "type": "string"
                      },
                      "OneLakePath": {
                          "type": "string"
                      },
                      "ProcessingPath": {
                          "type": "string"
                      },
                      "UniqueId": {
                          "type": "string"
                      }
                  },

- Lookup the last processed Change tracking version from the configuration database

  <img width="675" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/5d2f81b0-2a36-4da5-9d72-7879fa450172">


       Select * from table_store_ChangeTracking_version where TableName= '@{concat(pipeline().parameters.SchemaName,'.', pipeline().parameters.TableName)}'

- Select the current change tracking version

   <img width="797" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/137cec00-4832-4b14-8b84-54006716152a">

         SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion

- Add a copy activity to copy the incremental data between the previous change versions and current change versions

  <img width="806" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/7d29b002-9256-4f48-bd26-19851a8e3d1d">

             SELECT c.*,CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION , CT.@{pipeline().parameters.DeltaColumnName} as  'PrimaryKeyValue'
           
           from @{concat(pipeline().parameters.SchemaName,'.', pipeline().parameters.TableName)}  c
           
           RIGHT OUTER JOIN 
           
           CHANGETABLE(CHANGES @{concat(pipeline().parameters.SchemaName,'.', pipeline().parameters.TableName)} , @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) AS CT 
           
           ON c.@{pipeline().parameters.DeltaColumnName}  = CT.@{pipeline().parameters.DeltaColumnName}  where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}

            Destination file path would be @concat(pipeline().parameters.OneLakePath,pipeline().parameters.TableName,'/','year=',formatDateTime(utcnow(),'yyyy'),'/','month=',formatDateTime(utcnow(),'MM'),'/','day=',formatDateTime(utcnow(),'dd'),'/',pipeline().parameters.UniqueId)

- Update the current change tracking version to config tables for next run

    <img width="668" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/9d88627f-87c3-46d9-82e6-d4cc37ef09c5">

 - Log sucess and Failures and copy the file for further curation , Refer to CDC copy section for complete details on these steps 

##### Creating the Generic Watermark Load Pipeline

- Browse to your Fabric enabled workspace in Power Bi and switch to Data Factory and create a new pipeline

  <img width="388" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/5d27b4f1-b4a0-4ff0-9483-f6babc7b0cf6">

- Name the Pipeline related to Change Tracking , For example "PL_GENERIC_INGEST_WATERMARK"

- For understanding the flow of the pipeline refer [here](https://learn.microsoft.com/en-us/azure/data-factory/tutorial-incremental-copy-overview#delta-data-loading-from-database-by-using-a-watermark)
  
- Parameters for the pipeline are as below

           "parameters": {
                     "TableName": {
                         "type": "String"
                     },
                     "SchemaName": {
                         "type": "string"
                     },
                     "LakeHouseName": {
                         "type": "string"
                     },
                     "OneLakePath": {
                         "type": "string"
                     },
                     "DeltaColumnName": {
                         "type": "string"
                     },
                     "WaterMarkColumnName": {
                         "type": "string"
                     },
                     "ProcessingPath": {
                         "type": "String"
                     },
                     "UniqueID": {
                         "type": "string"
                     }
                 },
- Lookup the previous stored and current watermark values

    <img width="578" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/be646351-be19-4e13-a9ab-eb7572c3a0b4">

             Previous watermark value lookup - select * from dbo.table_store_watermark_value where TableName='@{concat(pipeline().parameters.SchemaName,'.', pipeline().parameters.TableName)}'

             Current watermark value lookup - select MAX(@{pipeline().parameters.WaterMarkColumnName}) as NewWatermarkvalue from @{concat(pipeline().parameters.SchemaName,'.', pipeline().parameters.TableName)}

- Copy the incremental data between the watermark values

  <img width="722" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/cfdfb894-93a4-4a75-b29a-fa36742b4a96">

            Copy Source query - select * from @{concat(pipeline().parameters.SchemaName,'.', pipeline().parameters.TableName)} where @{pipeline().parameters.WaterMarkColumnName} > '@{activity('Lookup Previous Watermark Value').output.firstRow.WatermarkValue}' and @{pipeline().parameters.WaterMarkColumnName} <= '@{activity('Lookup Current Watermark Value').output.firstRow.NewWatermarkvalue}'


            Destination File path - @concat(pipeline().parameters.OneLakePath,pipeline().parameters.TableName,'/','year=',formatDateTime(utcnow(),'yyyy'),'/','month=',formatDateTime(utcnow(),'MM'),'/','day=',formatDateTime(utcnow(),'dd'),'/',pipeline().parameters.UniqueID)

- Update the Latest watermark value to tables by executing the SP

     <img width="754" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/529eb8c8-8ecf-4d3f-b0d2-046ada5428bf">

 - Log success and Failures and copy the file for further curation , Refer to CDC copy section for complete details on these steps 

##### Creating the Generic Full Load Pipeline

- Browse to your Fabric enabled workspace in Power Bi and switch to Data Factory and create a new pipeline

  <img width="388" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/5d27b4f1-b4a0-4ff0-9483-f6babc7b0cf6">

- Name the Pipeline related to Full copy  , For example "PL_Generic_Full_Load"

- Parameters for the pipeline is listed below

            "parameters": {
                        "SchemaName": {
                            "type": "string"
                        },
                        "TableName": {
                            "type": "string"
                        },
                        "OneLakePath_or_TableName": {
                            "type": "string"
                        },
                        "PartitionColumnName": {
                            "type": "string"
                        },
                        "PartitionUpperBound": {
                            "type": "string"
                        },
                        "PartitionLowerBound": {
                            "type": "string"
                        },
                        "LakeHouseName": {
                            "type": "string"
                        }
                    },

- For the full load , the table in the Lakehouse is overwritten and no files are created

  <img width="539" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/2004987d-544a-4aaf-97fa-c55389deab17">


  
- Provide the lakehouse table name in the OneLakePath_or_TableName column in control table
  
- If the source table does not have physical partitions and we need parallel copy from the table then we can enable Dynamic range partition option , Refer below image for details , partition column parameters are listed below 

<img width="778" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/594b8f80-4744-46c7-b275-1a379842bc3b">

        @pipeline().parameters.PartitionColumnName , 
        @pipeline().parameters.PartitionUpperBound
        @pipeline().parameters.PartitionLowerBound

-  Log success and Failures , Refer to CDC copy section for complete details on these steps 

**Create Notebooks to Curate Bronze Layer Tables**

- we have created the incremental Parquet files from different change capture mechanism
  
- Each change capture mechanism has different schema in parquet files
  
-  Change Tracking , CDC can track deletes while Watermark cannot track deletes
  
-  We will create a generic notebook for each change capture mechanism to curate the Bronze layer table with Upserts and Deletes
  
-  The Notebooks would use the same metadata driven tables to curate the bronze tables

##### Creating the Bronze Curation For CDC Files 

- Browse to your Fabric enabled workspace in Power Bi and switch to Data Engineering and create a new notebook

<img width="826" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/cb892262-94d0-459c-8e29-017e728c6ab2">



- Name the notebook related to CDC curation example "GenericCurationCDC"

- Create the parameters for the notebook in the cell 

<img width="412" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/3e0a0440-e7ec-4aaa-88d6-355c71532574">


           #parameters
         processing_path= ""
         table_name= ""
         target_lakehousetablename= ""
         primaryKeyColumn= ""
         lakehousename=""

- Import neccassary packages and set configurations

          from delta.tables import *
        from pyspark.sql.window import Window
        from pyspark.sql.functions import row_number
        from pyspark.sql.functions import rank
        from pyspark.sql.functions import desc

                #configure vorder for Performance
        spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
        spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
        spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize", "1073741824")

- Read the Incremental CDC files created from the pipelines

          #get incremental data to be processed and rename sql cdc columns for clarity
        processing_filepath=""
        processing_filepath = lakehousename + '/Files/'+processing_path+table_name+"/"
        #processing_filepath = lakehousename + '/Files/'+processing_path
        df_sqlcdc = spark.read.parquet(processing_filepath).withColumnRenamed("__$operation","operation").withColumnRenamed("__$seqval","seqval").drop("__$start_lsn","__$update_mask")
        #display(df_sqlcdc)

- Read the delta table where upserts and deletes should happen

          #get delta table data to be processed
       tablequery = lakehousename +'/Tables/'+target_lakehousetablename
       #assumes Delta table exists , add validation for not exists
       if DeltaTable.isDeltaTable(spark,tablequery):
           df_table = DeltaTable.forPath(spark,tablequery)

- Create Dataframes that contain data for Updates , Deletes , Inserts from CDC files

         
           #This filters and saves the Insert data to a separate data frame
         inserted_rows_df = df_sqlcdc.filter("operation = '2'")
         windowSpec = Window.partitionBy(primaryKeyColumn).orderBy(
                 desc("seqval")
             )
         
         #This filters and saves the update data to a separate dataframe
         updated_rows_df_int = df_sqlcdc.filter("operation in ('3','4')").withColumn("rank",rank().over(windowSpec))
         #remove duplicates based on Key
         updated_rows_df = updated_rows_df_int.filter("rank == 1")
         
         #This filters and saves the delete data to a separate dataframe
         deleted_rows_df = df_sqlcdc.filter("operation = '1'")

  
- Execute the deletes , Updates , Inserts by joining the dataframes with the delta table

            #This deletes the delete incremental rows
          df_table.alias('inc') \
            .merge(
              deleted_rows_df.alias('update'),
              'inc.' + primaryKeyColumn + '=' + 'update.' + primaryKeyColumn
            ) \
           .whenMatchedDelete(
            ) \
            .execute()

                    #This inserts the insert incremental rows 
           #whenNotMatchedInsertAll and whenMatchedUpdateAll can be in  a single statement for clarity two sep statements
           df_table.alias('inc') \
             .merge(
               inserted_rows_df.alias('update'),
                'inc.' + primaryKeyColumn + '=' + 'update.' + primaryKeyColumn
             ) \
            .whenNotMatchedInsertAll(
             ) \
             .execute()

              #This updates the update incremental rows 
            df_table.alias('inc') \
              .merge(
                updated_rows_df.alias('update'),
                 'inc.' + primaryKeyColumn + '=' + 'update.' + primaryKeyColumn
              ) \
             .whenMatchedUpdateAll(
              ) \
              .execute()

- Remove the files processed

          #remove the files for current table  after processing to avoid re-processing
        from notebookutils import mssparkutils
        
        mssparkutils.fs.rm(processing_filepath, True)


  ##### Creating the Bronze Curation For Change Tracking Files 

- Browse to your Fabric enabled workspace in Power Bi and switch to Data Engineering and create a new notebook

<img width="826" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/cb892262-94d0-459c-8e29-017e728c6ab2">



- Name the notebook related to Change Tracking curation example "GenericCurationCH"

- Create the parameters for the notebook in the cell 

<img width="412" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/3e0a0440-e7ec-4aaa-88d6-355c71532574">


           #parameters
         processing_path= ""
         table_name= ""
         target_lakehousetablename= ""
         primaryKeyColumn= ""
         lakehousename=""

- Import neccassary packages and set configurations

          from delta.tables import *
        from pyspark.sql.window import Window
        from pyspark.sql.functions import row_number
        from pyspark.sql.functions import rank
        from pyspark.sql.functions import desc

                #configure vorder for Performance
        spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
        spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
        spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize", "1073741824")

- Read the Incremental Change Tracking files created from the pipelines

          #get incremental data to be processed
           processing_filepath=""
           processing_filepath = lakehousename +'/Files/'+processing_path+table_name+"/"
           print(processing_filepath)
           df_changetracking = spark.read.parquet(processing_filepath)
           #display(df_changetracking)

- Read the delta table where upserts and deletes should happen

          #get delta table data to be processed
       tablequery = lakehousename +'/Tables/'+target_lakehousetablename
       #assumes Delta table exists , add validation for not exists
       if DeltaTable.isDeltaTable(spark,tablequery):
           df_table = DeltaTable.forPath(spark,tablequery)

- Create Dataframes that contain data for Updates , Deletes , Inserts from Change Tracking files

         
                    #This filters and saves the Insert data to a separate dataframe
         inserted_rows_df = df_changetracking.filter("SYS_CHANGE_OPERATION = 'I'")
         windowSpec  = Window.partitionBy("PrimaryKeyValue").orderBy(desc("SYS_CHANGE_VERSION")) 
         
         #This filters and saves the update data to a separate dataframe
         updated_rows_df_int = df_changetracking.filter("SYS_CHANGE_OPERATION = 'U'").withColumn("rank",rank().over(windowSpec))
         #remove duplicates based on Key
         updated_rows_df = updated_rows_df_int.filter("rank == 1")
         
         #This filters and saves the delete data to a separate dataframe
         deleted_rows_df = df_changetracking.filter("SYS_CHANGE_OPERATION = 'D'")
         #display(deleted_rows_df)

- Execute the deletes , Updates , Inserts by joining the dataframes with the delta table

                  from delta.tables import *
                  
                  #This deletes the delete incremental rows
                  df_table.alias('inc') \
                    .merge(
                      deleted_rows_df.alias('update'),
                      'inc.' + primaryKeyColumn + '=' + 'update.primaryKeyValue' 
                    ) \
                   .whenMatchedDelete(
                    ) \
                    .execute()

                  #This inserts the insert incremental rows 
                 #whenNotMatchedInsertAll and whenMatchedUpdateAll can be in  a single statement for clarity two sep statements
                 df_table.alias('inc') \
                   .merge(
                     inserted_rows_df.alias('update'),
                      'inc.' + primaryKeyColumn + '=' + 'update.' + primaryKeyColumn
                   ) \
                  .whenNotMatchedInsertAll(
                   ) \
                   .execute()
                 
                 #This updates the update incremental rows 
                 df_table.alias('inc') \
                   .merge(
                     updated_rows_df.alias('update'),
                      'inc.' + primaryKeyColumn + '=' + 'update.' + primaryKeyColumn
                   ) \
                  .whenMatchedUpdateAll(
                   ) \
                   .execute()

  - Remove the files processed

          #remove the files for current table  after processing to avoid re-processing
        from notebookutils import mssparkutils
        
        mssparkutils.fs.rm(processing_filepath, True)

 ##### Creating the Bronze Curation For Watermarking files

- Browse to your Fabric enabled workspace in Power Bi and switch to Data Engineering and create a new notebook

<img width="826" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/cb892262-94d0-459c-8e29-017e728c6ab2">



- Name the notebook related to watermark curation exacmple "GenericCurationWatermark"

- Create the parameters for the notebook in the cell 

<img width="412" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/3e0a0440-e7ec-4aaa-88d6-355c71532574">


           #parameters
              processing_path= ""
              table_name= ""
              target_lakehousetablename= ""
              primaryKeyColumn= ""
              watermarkKeyColumn=""
              lakehousename=""

- Import necessary packages and set configurations

          from delta.tables import *
        from pyspark.sql.window import Window
        from pyspark.sql.functions import row_number
        from pyspark.sql.functions import rank
        from pyspark.sql.functions import desc

                #configure vorder for Performance
        spark.conf.set("spark.sql.parquet.vorder.enabled", "true")
        spark.conf.set("spark.microsoft.delta.optimizeWrite.enabled", "true")
        spark.conf.set("spark.microsoft.delta.optimizeWrite.binSize", "1073741824")

- Read the Incremental Change Tracking files created from the pipelines

             #get incremental data to be processed and rename sql cdc columns for clarity
             processing_filepath=""
             #processing_filepath = '/lakehouse/'+ lakehousename +'/Files/'+processing_path+table_name+'/'
             processing_filepath = lakehousename +'/Files/'+processing_path+table_name+'/'
             
             df_watermark = spark.read.parquet(processing_filepath)

- Read the delta table where upserts and deletes should happen

          #get delta table data to be processed
       tablequery = lakehousename +'/Tables/'+target_lakehousetablename
       #assumes Delta table exists , add validation for not exists
       if DeltaTable.isDeltaTable(spark,tablequery):
           df_table = DeltaTable.forPath(spark,tablequery)

- Create Dataframes that contain data for Updates , Inserts from Change Tracking files

         
                windowSpec = Window.partitionBy(primaryKeyColumn).orderBy(desc(watermarkKeyColumn))

               #This filters and saves the update data to a separate dataframe
               insertedupdated_rows_df_int = df_watermark.withColumn("rank",rank().over(windowSpec))
               #remove duplicates based on Key
               ins_updated_rows_df = insertedupdated_rows_df_int.filter("rank == 1")


- Execute the Updates , Inserts by joining the dataframes with the delta table

                               #This inserts/updates the insert incremental rows 
               #whenNotMatchedInsertAll and whenMatchedUpdateAll 
               df_table.alias('inc') \
                 .merge(
                   ins_updated_rows_df.alias('update'),
                    'inc.' + primaryKeyColumn + '=' + 'update.' + primaryKeyColumn
                 ) \
                .whenNotMatchedInsertAll(
                 ) \
                 .whenMatchedUpdateAll(
                 ) \
                 .execute()
- Remove the files processed

          #remove the files for current table  after processing to avoid re-processing
        from notebookutils import mssparkutils
        
        mssparkutils.fs.rm(processing_filepath, True)
  
 ##### Create the Orchestration Pipeline to execute the notebooks 

*The idea behind having separate pipeline for notebook execution is to separate the ingestion from curation* 
 - Browse to your Fabric enabled workspace in Power Bi and switch to Data Factory and create a new pipeline

  <img width="388" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/5d27b4f1-b4a0-4ff0-9483-f6babc7b0cf6">

- Name the Pipeline related to Bronze Curation , For example "PL_Curate_Bronze"
  
- Lookup the tables which have successful ingestion completed and ready to curated.

 <img width="703" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/06f6c6c9-8d0f-498d-9f3f-f7dbed7637f5">

           SELECT distinct ctl.*
           FROM [dbo].[IngestionLog] loging (nolock)
           join [dbo].[ControlTable] ctl on  concat(ctl.schemaname,'.',ctl.SourceTableName) = loging.TableName
           where RunStatus='Success' and ctl.CopyMode in ('WATERMARK',
         'CHANGE TRACKING','CDC') and IsActive='Y'
           
           and CAST(UpdateDate as date) >= CAST(GETDATE() As Date) 

- Add a Foreach activity to curate each table

<img width="451" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/f09574c9-572d-4e6a-8491-c2e1fa2c97d1">

         Items property would be @activity('Lookup Config Table').output.value

- Within the For Each Actvity add a switch activity based on the copymode  to switch and execute the corresponding  notebooks created in previous section
    -  Example if CopyMode is WATERMARK then Execute the Watermark notebook by passing the parameters , Execute CDC , Change Tracking notebooks in a similar fashion
              @item().CopyMode
 
  <img width="1000" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/1a81180b-c8d3-4092-a667-bd166d987e7e">


     <img width="952" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/8198b9f3-4879-4272-97bd-4504bb4d083c">


                    "parameters": {
                                                        "processing_path": {
                                                            "value": {
                                                                "value": "@item().processingpath",
                                                                "type": "Expression"
                                                            },
                                                            "type": "string"
                                                        },
                                                        "table_name": {
                                                            "value": {
                                                                "value": "@item().SourceTableName",
                                                                "type": "Expression"
                                                            },
                                                            "type": "string"
                                                        },
                                                        "target_lakehousetablename": {
                                                            "value": {
                                                                "value": "@item().SourceTableName",
                                                                "type": "Expression"
                                                            },
                                                            "type": "string"
                                                        },
                                                        "primaryKeyColumn": {
                                                            "value": {
                                                                "value": "@item().DeltaColumnName",
                                                                "type": "Expression"
                                                            },
                                                            "type": "string"
                                                        },
                                                        "watermarkKeyColumn": {
                                                            "value": {
                                                                "value": "@item().WaterMarkColumnName",
                                                                "type": "Expression"
                                                            },
                                                            "type": "string"
                                                        },
                                                        "lakehousename": {
                                                            "value": {
                                                                "value": "@item().LakeHouseName",
                                                                "type": "Expression"
                                                            },
                                                            "type": "string"
                                                        }
                                                    },
                                                    
 ##### Create the Orchestration Pipeline to execute the Ingestion and notebooks 

- Browse to your Fabric enabled workspace in Power Bi and switch to Data Factory and create a new pipeline

  <img width="388" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/5d27b4f1-b4a0-4ff0-9483-f6babc7b0cf6">

- Name the Pipeline related to Ingestion and Bronze Curation , For example "PL_Generic_Ingestion_Executor"
  
- Lookup the tables which are ready to be ingested

  <img width="981" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/818fcebf-1c0e-4149-b5c1-2f65b46fb25d">
        
          SELECT *
          FROM dbo.ControlTable where isActive='Y'

- Add a Foreach activity to copy data from each table in a parallel fashion

   <img width="1045" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/47c42a3e-01d9-408b-b830-de2acc06a63f">

         @activity('Get Tables to Load from Config').output.value
 
 - Within the For Each Activity add a switch activity based on the copymode and based on copymode execute corresponding pipeline created in previous steps , Ensure Wait on completion is checked to force the activity to wait until completion 
   
    -  For Example if the copymode is CDC then execute the CDC ingestion pipeline
  
      <img width="1069" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/9052ac4d-944b-4a7d-a53f-e4e6ff17dc9b">
      
      <img width="876" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/8b78a695-e43b-490d-9a94-9b12e8d121ec">

- The parameters are as below

              "parameters": {
                                                        "SchemaName": {
                                                            "value": "@item().SchemaName",
                                                            "type": "Expression"
                                                        },
                                                        "TableName": {
                                                            "value": "@item().SourceTableName",
                                                            "type": "Expression"
                                                        },
                                                        "OneLakePath_or_TableName": {
                                                            "value": "@item().OneLakePath_or_TableName",
                                                            "type": "Expression"
                                                        },
                                                        "PartitionColumnName": {
                                                            "value": "@item().PartitionColumnName",
                                                            "type": "Expression"
                                                        },
                                                        "PartitionUpperBound": {
                                                            "value": "@item().PartitionUpperBound",
                                                            "type": "Expression"
                                                        },
                                                        "PartitionLowerBound": {
                                                            "value": "@item().PartitionLowerBound",
                                                            "type": "Expression"
                                                        },
                                                        "LakeHouseName": {
                                                            "value": "@item().LakehouseArtifactID",
                                                            "type": "Expression"
                                                        }
                                                    }
                                                }
                                            }

        <img width="890" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/8a24584c-da6a-489f-b63c-bc9a97cb6cf0">


- Add a delay of few seconds to ensure the files are written and available for Notebooks to read

- Execute the Notebook execution pipeline and send email after completion

  <img width="797" alt="image" src="https://github.com/mahes-a/StagingBuild/assets/120069348/4afd156a-492f-4fa3-a5c5-b9e063ed7315">

- The entire template is ready for testing

