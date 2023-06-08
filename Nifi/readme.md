# Nifi Base Flow Breakdown
### Contents
* [Ingestion and Tagging](#Ingestion-and-Tagging)
* [Routing and Schema Inference](#Routing-and-Schema-Inference)
* [Uploading to Sentinel](#Uploading-to-Sentinel)
* [Uploading to Data Lake](#Uploading-to-Data-Lake)
<br><br>

## Ingestion and Tagging
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/848ae26c-1593-49b4-966c-bc9a8e57012a">
<br><br>

### ListenSyslog
We start with listening for Syslog messages. There are two listen syslog processors, one for UDP and one for TCP, each listening on a different port. 
<br>
The syslog messages are handled individually. This lets the processor extract the syslog headers and syslog body and store those values into flowfile attributes. The extracted syslog body attribute, syslog.body, will later be used for tagging each flowfile acccording to the message type.
<br>

### Tag on Log Type
Now that the syslog body is available in an attribute, we can tag the flowfile according to the syslog message content. This processor uses rules that look for matches in the syslog.body attribute. According to the match they find they add a new attribute called sender.type. 
<br>
After adding this attribute the processor removes the longer syslog.body attribute. We no longer need this attribute since the flowfile attributes now have what they need for downstream routing.
  <br>

<img width="1000" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/6ac03fd2-a8fa-4f4b-9395-31b561eb44a9">
<br>    
    
### Merge by Log Type
Records with the same sender.type are merged together. This batching increases efficiency downstream.
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/ae2bef3a-0b59-4bc7-8eca-1912181e1b37">
<br>    
<br>    
<br>    
  
## Routing and Schema Inference
<img width="1000" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/0492fa2c-e203-424a-b6b5-22ca55ab6a3b">
<br>    

### Route - Log Sender and Attribute
Records are sent to different output groups based on the sender.type attribute. This is to enable further routing downstream and schema inference.
<br>    

### Schema Inference
<br>    
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/8e85f06d-b83e-402c-9b7b-5eb8eff25192">
<br>    

This processor group allows for asynchronous schema inference. This reduces the load of real time full schema inference while also preventing the need to manually add and update a schema.
This works best for log types with relatively consitent schemas, or log types for which the full schema can be represented by a sample of the logs.
<br>    

#### Control Rate
Data is sampled using this processor group. This sampling rate can be increased to account for more volatile schemas.

<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/17f9089d-7738-4ee7-80f7-773476fe9d52">


<br>    

#### Schema Inference and Cache Storage
The schema is inferred with the full batch of sampled data and stored into the avro.schema attribute. This attribute then replaces the full flowfile text in the replacetext processor. Then this whole flowfile is stored in cache with the identifier being the same sender.type attribute.
<br>    

<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/c74dc641-1fc0-4bdb-a777-7b232d2d96dd">  
<br>
<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/328c8373-d02c-448d-a618-652d2e60c818">    
<br>
<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/97897264-6a1d-48c8-aff4-893445f0f21d">    
<br>
<br>    
<br>    

## Uploading to Sentinel
Logs with immediate security value is sent directly to Sentinel. The output group containing these logs are sent to the Sentinel processor group from the routing group above. 
<br>    

### Batch Convert to JSON
The logs are converted from CEF to JSON. The record reader can use realtime schema inference, manual schema lookup, or a [asynchronous schema lookup](#Schema-Lookup) explained further below. The size is set to 500 KB to make sure we don't hit Sentinel ingestion limits.  
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
### Schema Lookup
Each pipeline looks up its schema from cache with the identifier being that file's sender.type. This is retried until the schema is found since the schema may take a minute to poulate.
<br>
<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/4149c3e1-3c88-4025-aecf-8d9645eacc31">
<br><br>
### Batch to Parquet and Batch 1 Minute
Batching is done twice. First the records are converted to parquet and batched for a total of 10-10000 records, then the parquet files are batched into 1 minute bins with a max batch size of 50000000 records.
<br><br> Two merges are used to avoid the first merge filling up the heap space while waiting for the one minute bin, as recommended [here](https://nifi.apache.org/docs/nifi-docs/components/org.apache.nifi/nifi-standard-nar/1.5.0/org.apache.nifi.processors.standard.MergeRecord/additionalDetails.html)
<br><br>
### Send to Data Lake
We are now ready to upload our logs to the data lake, each sender.type getting its own container. This is done in a yy-MM-dd/HH/mm format. Authentication to the data lake can be done with a key, SAS token, managed identity, or service principal client ID and secret.




