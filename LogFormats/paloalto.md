## Components
* [Traffic](#Traffic)
* [Threat](#Threat)
* [URL](#URL)
* [Wildfire](#Wildfire)
* [System](#Wildfire)

## Traffic
### Nifi

#### 8.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|1|rt=$cef-formatted-receive_time src=$src dst=$dst cs1=$rule suser=$srcuser duser=$dstuser ApplicationProtocol=$app cs4=$from cs5=$to SourcePort=$sport DestinationPort=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action flexNumber1=$bytes total_in=$bytes_sent total_out=$bytes_received cn3=$elapsed cs2=$category reason=$session_end_reason DeviceName=$device_name
#### 9.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|1|rt=$cef-formatted-receive_time src=$src dst=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if spt=$sport dpt=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action flexNumber1=$bytes in=$bytes_sent out=$bytes_received cn3=$elapsed cs2=$category reason=$session_end_reason DeviceName=$device_name
#### 10.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|1|rt=$cef-formatted-receive_time src=$src dst=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if spt=$sport dpt=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action flexNumber1=$bytes in=$bytes_sent out=$bytes_received cn3=$elapsed cs2=$category reason=$session_end_reason DeviceName=$device_name

## Threat
### Nifi
#### 8.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|rt=$cef-formatted-receive_time src=$src dst=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to spt=$sport dpt=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid DeviceName=$device_name 

#### 9.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$threatid|$type|$number-of-severity|rt=$cef-formatted-receive_time src=$src dst=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt spt=$sport dpt=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid DeviceName=$device_name PanOSThreatCategory=$thr_category PanOSURLCatList=$url_category_list

#### 10.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$threatid|$type|$number-of-severity|rt=$cef-formatted-receive_time src=$src dst=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt spt=$sport dpt=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid DeviceName=$device_name PanOSThreatCategory=$thr_category PanOSURLCatList=$url_category_list


## URL
### Nifi
#### 8.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|rt=$cef-formatted-receive_time src=$src dst=$dst destinationTranslatedAddress=$natdst cs1Label=Rule cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to spt=$sport dpt=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid requestMethod=$http_method requestClientApplication=$user_agent DeviceName=$device_name 

#### 9.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|rt=$cef-formatted-receive_time src=$src dst=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt spt=$sport dpt=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid requestMethod=$http_method requestClientApplication=$user_agent PanOSXForwarderfor=$xff PanOSReferer=$referer DeviceName=$device_name PanOSThreatCategory=$thr_category PanOSURLCatList=$url_category_list

#### 10.x
CEF:0|Palo Alto Networks|PAN-OS|$sender_sw_version|$subtype|$type|$number-of-severity|rt=$cef-formatted-receive_time src=$src dst=$dst cs1=$rule suser=$srcuser duser=$dstuser app=$app cs4=$from cs5=$to deviceInboundInterface=$inbound_if deviceOutboundInterface=$outbound_if cnt=$repeatcnt spt=$sport dpt=$dport flexString1=$flags transportprotocol=$proto SimplifiedDeviceAction=$action request=$misc cs2=$category flexString2=$direction cat=$threatid requestMethod=$http_method requestClientApplication=$user_agent PanOSXForwarderfor=$xff PanOSReferer=$referer DeviceName=$device_name PanOSThreatCategory=$thr_category PanOSURLCatList=$url_category_list

## Wildfire
### Nifi


## VPN
### Nifi

## System
### Nifi

