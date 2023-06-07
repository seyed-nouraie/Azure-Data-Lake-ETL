# Nifi Base Flow Breakdown

## Ingestion and Tagging
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/848ae26c-1593-49b4-966c-bc9a8e57012a">


### ListenSyslog
We start with listening on for Syslog. There are two listen syslog processors, one for UDP and one for TCP, each listening on different ports. 
The syslog messages are handled individually so that the processor can parses the headers and syslog body and store their values into flowfile attribute. The syslog body attribute (syslog.body), will be used for tagging each flowfile acccording to the message type.


### Tag on Log Type
Now that the syslog body is available in an attribute, we can tag the flowfile according to its content. This processor uses rules that look for matches in the syslog.body attribute. According to the match they find they add a new attribute called sender.type. After adding this attribute the processor removes the longer, full syslog.body attribute since the flowfile attributes now have what they need for downstream routing.
  
<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/5fa34974-1156-4a05-9867-4732aab8e8b3">

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

<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/328c8373-d02c-448d-a618-652d2e60c818">

<img width="500" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/97897264-6a1d-48c8-aff4-893445f0f21d">











