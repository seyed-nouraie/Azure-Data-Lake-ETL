# Nifi Base Flow Breakdown
### Contents
* [Ingestion and Tagging](#Ingestion-and-Tagging)
* [Routing and Schema Inference](#Routing-and-Schema-Inference)




## Ingestion and Tagging
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/848ae26c-1593-49b4-966c-bc9a8e57012a">


### ListenSyslog
We start with listening for Syslog messages. There are two listen syslog processors, one for UDP and one for TCP, each listening on a different port. 

The syslog messages are handled individually. This lets the processor extract the syslog headers and syslog body and store those values into flowfile attributes. The extracted syslog body attribute, syslog.body, will later be used for tagging each flowfile acccording to the message type.


### Tag on Log Type
Now that the syslog body is available in an attribute, we can tag the flowfile according to the syslog message content. This processor uses rules that look for matches in the syslog.body attribute. According to the match they find they add a new attribute called sender.type. 

After adding this attribute the processor removes the longer syslog.body attribute. We no longer need this attribute since the flowfile attributes now have what they need for downstream routing.
  

<img width="1000" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/6ac03fd2-a8fa-4f4b-9395-31b561eb44a9">
  
    
### Merge by Log Type
Records with the same sender.type are merged together. This batching increases efficiency downstream.
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/ae2bef3a-0b59-4bc7-8eca-1912181e1b37">
  
  
## Routing and Schema Inference
<img width="1000" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/0492fa2c-e203-424a-b6b5-22ca55ab6a3b">

### Route - Log Sender and Attribute
Records are sent to different output groups based on the sender.type attribute. This is to enable further routing downstream and schema inference.

### Schema Inference
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/8e85f06d-b83e-402c-9b7b-5eb8eff25192">

This processor group allows for asynchronous schema inference. This reduces the load of real time full schema inference while also preventing the need to manually add and update a schema.
This works best for log types with relatively consitent schemas, or log types for which the full schema can be represented by a sample of the logs.

#### Control Rate
Data is sampled using this processor group. This sampling rate can be increased to account for more volatile schemas.

<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/17f9089d-7738-4ee7-80f7-773476fe9d52">



#### Schema Inference and Cache Storage
The schema is inferred with the full batch of sampled data and stored into the avro.schema attribute. This attribute then replaces the full flowfile text in the replacetext processor. Then this whole flowfile is stored in cache with the identifier being the same sender.type attribute.

<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/c74dc641-1fc0-4bdb-a777-7b232d2d96dd">  
##
<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/328c8373-d02c-448d-a618-652d2e60c818">    
##
<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/97897264-6a1d-48c8-aff4-893445f0f21d">    
##

## Sentinel
Logs with immediate security value is sent directly to Sentinel. The output group containing these logs are sent to the Sentinel processor group from the routing group above. 

### Batch Convert to JSON
The logs are converted from CEF to JSON. The record reader can use realtime schema inference, manual schema lookup, or a asynchronous schema lookup explained further below. The size is set to 500 KB to make sure we don't hit Sentinel ingestion limits.  

<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/0acf1aaa-fe83-4939-a252-c3ada166f84d">

### Azure Monitor HTTP API
A data collection endpoint (DCE) and data collection rule (DCR) have already been created in Azure using [this tutorial](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/tutorial-logs-ingestion-portal). This processor sends logs to Azure monitor using that DCE. The processor performs authentication using an Oauth2 controller service, which uses Azure client credentials that have been granted access to that DCR.  

A DCR can also be configured with [transformations](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/data-collection-transformations#single-destination) to send data to [built in tables](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/logs-ingestion-api-overview#supported-tables).  

API Call HTTP URL: https://<DCE_URI>/dataCollectionRules/<DCR_ID>/streams/<STREAM_NAME>?api-version=2021-11-01-preview 

Authorization Server URL: https://login.microsoftonline.com/<TENANT_ID>/oauth2/v2.0/token  

Scope: https://monitor.azure.com//.default  


<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/791a855e-462d-4ac4-ac93-a2b3f8067f9a">

<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/759891f3-1f12-41f3-b921-2656884a9f99">






