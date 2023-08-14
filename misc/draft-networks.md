## What are networks?
Systems connected to each other to share resources/data etc.

## Internet standards
Important for interoperability.  
Made through [RFCs](https://en.wikipedia.org/wiki/Request_for_Comments), handled by IETF (Internet Engineering Task Force).  
Sample: [RFC 791](https://www.rfc-editor.org/pdfrfc/rfc791.txt.pdf), protocol spec.  

## Protocols
TCP: Allows communication, ensures message is sent and isn't corrupted.  
UDP: Allows communication, does not provide guarantees about delivery and integrity.  
HTTP: Defines the format of messages for communication.  

Data is sent via packets to clients identified by IP addresses.  
IPv4 addresses are 32-bit numbers, broken into 8-bit numbers (0-255) separated by periods.  

To find our IP address:  
```bash
curl ifconfig.me -s
```

Ports identify the specific application running on the host - a host identified by an IP could be running many distinct applications at once.  
Every application has a 16-bit (0 - 65535) port number.  
General segregation: 0-1024 for specific applications aka ["well-known ports"](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers#Well-known_ports) (like HTTP ports), 1024-49152 for proprietary applications (like PG or MSSQL), and 49152-65535 for any other purposes.  

## Devices
### Hosts
All hosts connected to the internet excluding routing devices are called end-systems.  
The Network Interface Adapter are the physical gateways that connect computers to the internet.

### Access networks
These "last mile" devices (edge-routers) connect hosts to the internet. Eg: Routers, WiFi modems, etc

