# Nifi Base Flow Breakdown

## Ingestion and Tagging
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/848ae26c-1593-49b4-966c-bc9a8e57012a">

### ListenSyslog
There are two listen syslog processors, one for UDP and one for TCP, listening on different ports. The syslog messages are parsed individually and delimited with a newline character. This is so that they can each be tagged according to their content. 
These processors store the syslog headers and body in flowfile attributes.

### Tag on Log Type
This processor uses the syslog body attribute to tag the data with a new, shorter attribute to use for later routing. Then it drops the syslog body attribute.
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/5fa34974-1156-4a05-9867-4732aab8e8b3">

Tagging is done based on advanced rules in the processor. The advanced rule matches the syslog body and assigns the sender.type attribute with the value devicetype.logtype:
<img width="1000" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/6ac03fd2-a8fa-4f4b-9395-31b561eb44a9">

### Merge by Log Type
Records with the same sender.type are batched into single flowfiles. This increases efficiency downstream.
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/ae2bef3a-0b59-4bc7-8eca-1912181e1b37">


## Routing and Schema Inference
<img width="800" alt="image" src="https://github.com/seyed-nouraie/Azure-Data-Lake-ETL/assets/75258742/0492fa2c-e203-424a-b6b5-22ca55ab6a3b">





