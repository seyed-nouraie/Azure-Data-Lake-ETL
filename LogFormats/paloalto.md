## Components
* [Traffic](#Traffic)
* [Threat](#Threat)
* [URL](#URL)
* [Wildfire](#Wildfire)
* [System](#Wildfire)

## Traffic
### Nifi
#### 8.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|1|rt=$cef-formatted-receive_time srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser ApplicationProtocol=$app cs4=$from cs5=$to SourcePort=$sport DestinationPort=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action flexNumber1=$bytes total_in=$bytes_sent total_out=$bytes_received cn3=$elapsed cs2=$category reason=$session_end_reason DeviceName=$device_name

#### 9.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|1|rt=$cef-formatted-receive_time srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser ApplicationProtocol=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if SourcePort=$sport DestinationPort=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action flexNumber1=$bytes in=$bytes_sent out=$bytes_received cn3=$elapsed cs2=$category reason=$session_end_reason DeviceName=$device_name

#### 10.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|1|rt=$cef-formatted-receive_time srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser ApplicationProtocol=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if SourcePort=$sport DestinationPort=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action flexNumber1=$bytes in=$bytes_sent out=$bytes_received cn3=$elapsed cs2=$category reason=$session_end_reason DeviceName=$device_name

## Threat
### Nifi
#### 8.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|rt=$cef-formatted-receive_time srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser ApplicationProtocol=$app cs4=$from cs5=$to SourcePort=$sport DestinationPort=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid DeviceName=$device_name 

#### 9.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$threatid|$type|$number-of-severity|rt=$cef-formatted-receive_time srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt SourcePort=$sport DestinationPort=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid DeviceName=$device_name PanOSThreatCategory=$thr_category PanOSURLCatList=$url_category_list

#### 10.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$threatid|$type|$number-of-severity|rt=$cef-formatted-receive_time srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt SourcePort=$sport DestinationPort=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid DeviceName=$device_name PanOSThreatCategory=$thr_category PanOSURLCatList=$url_category_list


## URL
### Nifi
#### 8.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|rt=$cef-formatted-receive_time srcip=$src dstip=$dst destinationTranslatedAddress=$natdst cs1Label=Rule cs1=$rule suser=$srcuser duser=$dstuser ApplicationProtocol=$app cs4=$from cs5=$to SourcePort=$sport DestinationPort=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid requestMethod=$http_method requestClientApplication=$user_agent DeviceName=$device_name 

#### 9.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|rt=$cef-formatted-receive_time srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser ApplicationProtocol=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt SourcePort=$sport DestinationPort=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid requestMethod=$http_method requestClientApplication=$user_agent PanOSXForwarderfor=$xff PanOSReferer=$referer DeviceName=$device_name PanOSThreatCategory=$thr_category PanOSURLCatList=$url_category_list

#### 10.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|rt=$cef-formatted-receive_time srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser ApplicationProtocol=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt SourcePort=$sport DestinationPort=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid requestMethod=$http_method requestClientApplication=$user_agent PanOSXForwarderfor=$xff PanOSReferer=$referer DeviceName=$device_name PanOSThreatCategory=$thr_category PanOSURLCatList=$url_category_list

##Additional Logs
#### 10.x
### Data
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt spt=$sport dpt=$dport flexString1=$flags proto=$proto act=$action request=$misc cs2=$category flexString2=$direction dvchost=$device_name PanOSThreatCategory=$thr_category

### Wildfire
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt spt=$sport dpt=$dport flexString1=$flags proto=$proto act=$action request=$misc cs2=$category flexString2=$direction PanOSActionFlags=$actionflags cat=$threatid filePath=$cloud fileHash=$filedigest fileType=$filetype suid=$sender msg=$subject duid=$recipient dvchost=$device_name PanOSThreatCategory=$thr_category

### Config
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$result|$type|1|deviceExternalId=$serial shost=$host act=$cmd duser=$admin destinationServiceName=$client msg=$path dvchost=$device_name cs1=$before-change-detail cs2=$after-change-detail

### System
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|fname=$object flexString2=$module msg=$opaque cat=$eventid dvchost=$device_name

##Additional Logs
#### 9.x
### Data
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|srcip=$src dstip=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt spt=$sport dpt=$dport flexString1=$flags proto=$proto act=$action request=$misc cs2=$category flexString2=$direction dvchost=$device_name PanOSThreatCategory=$thr_category

### Wildfire
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|srcip=$src dst=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt spt=$sport dpt=$dport flexString1=$flags proto=$proto act=$action request=$misc cs2=$category flexString2=$direction PanOSActionFlags=$actionflags cat=$threatid filePath=$cloud fileHash=$filedigest fileType=$filetype suid=$sender msg=$subject duid=$recipient dvchost=$device_name PanOSThreatCategory=$thr_category

### Config
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$result|$type|1|deviceExternalId=$serial shost=$host act=$cmd duser=$admin destinationServiceName=$client msg=$path dvchost=$device_name cs1=$before-change-detail cs2=$after-change-detail

### System
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|fname=$object flexString2=$module msg=$opaque cat=$eventid dvchost=$device_name
