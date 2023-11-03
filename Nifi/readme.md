# Nifi Base Flow Breakdown
### Contents
* [Ingestion and Routing](#Ingestion-and-Routing)
* [Ad-Hoc Schema Inference](#Schema-Inference)
* [Uploading to Sentinel](#Uploading-to-Sentinel)
* [Uploading to Data Lake](#Uploading-to-Data-Lake)
<br><br>

## Ingestion and Routing
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/4acac6af-92de-4191-879e-009b7c1b05e9">
<br><br>

### Listeners
We start with listening for messages. There are two processors, one for UDP and one for TCP, each listening on a different port. We do not use a listensyslog to decouple ingestion and parsing. This helps avoid log dropping with traffic spikes.
<br>

### Route Text

<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/b7e926aa-8ba8-44d8-a68e-12617c7c87c6">
We use Route Text to send different log types to different downstream pipelines for parquet conversion and landing in their own containers in the data lake. If your sources all contain the same log type (ie syslog, JSON...) you can use the Query Record streaming processor for faster routing. 
<br>


### Schema Inference
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/6c175866-1832-42d7-b438-40b4ca8a0fda">

We use a Merge Record processor for Ad Hoc schema inference. The record writer must match the input of the data you expect with the schema inferred and the record writer should be JSON that writes the schema into the FlowFile attributes. 
<br>
When ingesting a new log type, we route the log to this processor, and view the attributes of the outputted FlowFiles to gather the schema. We will use this schema in the processors below.
<br>    
<br>    
<br>    



## Uploading to Sentinel
Logs with immediate security value is sent directly to Sentinel. The output group containing these logs are sent to the Sentinel processor group from the routing group above. 
<br>    

### Batch Convert to JSON
The logs are converted from CEF to JSON. The record reader should use the schema inferred in the step above. Always validate the schema. The size is set to 500 KB to make sure we don't hit Sentinel ingestion limits.  
<br>    

<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/0acf1aaa-fe83-4939-a252-c3ada166f84d">
<br>    

### Azure Monitor HTTP API
A data collection endpoint (DCE) and data collection rule (DCR) have already been created in Azure using [this tutorial](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/tutorial-logs-ingestion-portal). This processor sends logs to Azure monitor using that DCE. The processor performs authentication using an Oauth2 controller service, which uses Azure client credentials that have been granted access to that DCR.  

A DCR can also be configured with [transformations](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/data-collection-transformations#single-destination) to send data to [built in tables](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/logs-ingestion-api-overview#supported-tables).  
<br>    

##### API Call Parameters
API Call HTTP URL: https://<DCE_URI>/dataCollectionRules/<DCR_ID>/streams/<STREAM_NAME>?api-version=2021-11-01-preview 

##### OAuth Parameters
Authorization Server URL: https://login.microsoftonline.com/<TENANT_ID>/oauth2/v2.0/token  

Scope: https://monitor.azure.com//.default  

<br>    

<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/791a855e-462d-4ac4-ac93-a2b3f8067f9a">
<br>
<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/759891f3-1f12-41f3-b921-2656884a9f99">



## Uploading to Data Lake
All logs are loaded to adls. Each log sender.type gets its own queue since each has its own schema.
<br>
<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/32e884a3-4d62-4fa4-80bf-904f39100598">
<br>
<br>
### Schema
Each pipeline is given the schema that we inferred earlier from the log type. Ensure that the schema aligns to what you expect the source data to look like.
<br>
<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/22a1ca9a-3f02-4e9c-b44a-cb82a2d4e8fb">
<br><br>
### Batch to Parquet and Batch 1 Minute
Batching is done twice. First the records are converted to parquet and batched for a total of 10-10000 records, then the parquet files are batched into 1 minute bins with a max batch size of 50000000 records.
<br><br> Two merges are used to avoid the first merge filling up the heap space while waiting for the one minute bin, as recommended [here](https://nifi.apache.org/docs/nifi-docs/components/org.apache.nifi/nifi-standard-nar/1.5.0/org.apache.nifi.processors.standard.MergeRecord/additionalDetails.html)
<br><br>
### Send to Data Lake
We are now ready to upload our logs to the data lake, each sender.type getting its own container. This is done in a yy-MM-dd/HH/mm format. Authentication to the data lake can be done with a key, SAS token, managed identity, or service principal client ID and secret.




