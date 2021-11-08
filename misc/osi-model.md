# OSI model
The open systems interconnection (OSI) model is a conceptual model providing a standard for different computer systems to be able to communicate with each other.  

The OSI protocol suite that was specified as part of the OSI project was considered by many as too complicated and inefficient, and to a large extent unimplementable.  
The protocols included so many optional features that many vendors' implementations were not interoperable.  
**Although the OSI model is often still referenced, the Internet protocol suite has become the standard for networking.**

## Layers (top-down)  
|#|Layer|Protocol Data Unit|TLDR|
|:---:|:---:|:---:|:---:|
|7|Application Layer|Data|Human-computer interaction|
|6|Presentation Layer|Data|Converts data to a usable format|
|5|Session Layer|Data|Controls  connections, sessions and ports|
|4|Transport Layer|Segment, Datagram|Transmits data|
|3|Network Layer|Packet|Decides physical path of data|
|2|Data Link Layer|Frame|Defines format of data on the network|
|1|Physical Layer|Bit, Symbol|Transmits raw bits|

### 7 | Application Layer
This is the only layer that directly interacts with data from the user.  
client software applications are not part of the application layer; rather the application layer is responsible for the protocols and data manipulation that the software relies on to present meaningful data to the user.  
(Client applications fall outside the layer's scope)
Eg: HTTP, SMTP

### 6 | Presentation/Syntax Layer
Responsible for preparing data so that it can be used by the application layer, such as:  
- **Translation/Serialization:** Translates incoming data into a syntax that the application layer of the receiving device can understand. 
- **Encryption:** If connection between devices is encrypted, this layer encrypts data on the sender's end and decrypts data on the receiver's end.
- **Compression:** Compresses data before delivering it; improves the speed and efficiency by minimizing the amount of data that will be transferred.

### 5 | Session Layer
Responsible for opening and closing communication between the two devices.  
It also synchronizes data transfer with checkpoints, so that transfer of large files can resume from the last checkpoint in case of a crash or disconnection.  
Commonly implemented explicitly in application environments that use remote procedure calls. 

### 4 | Transport Layer
Responsible for end-to-end communication between the two devices.  
Takes data from the session layer and breaks it up into chunks called **segments**, and builds it up again at the receiving end.

In inter-network communication, it's also responsible for: 
- **Flow control:** Determines an optimal speed of transmission to ensure that a sender with a fast connection doesn’t overwhelm a receiver with a slow connection.
- **Error control:** Performs error control on the receiving end by ensuring that the data received is complete, and requesting a retransmission if it isn’t.

Reliability isn't a strict requirement of this layer (hence UDP, VoIP).  
OSI defines 5 classes, from class0 (fewest features, no error-recovery, built for error-free connections) to class4 (most features, built for less reliable networks like the internet).
Eg: TCP, UDP, VoIP

### 3 | Network Layer
Responsible for facilitating data transfer between two different networks - it finds the best physical path for the data to reach its destination; this is known as routing.  
If the two devices communicating are on the same network, then the network layer is unnecessary.  
Breaks up segments from the transport layer into smaller units called **packets**.  

### 2 | Data Link Layer
Similar to the network layer, except the data link layer facilitates data transfer between two devices **on the same network** - ie., node-to-node transfer.  
It takes packets from the network layer and breaks them into smaller pieces called **frames**.  
It is responsible for flow control and error control in intra-network communication.  

Sub-layers:  
- **Medium access control (MAC) layer:** Controls how devices in a network gain access to a medium and permission to transmit data.  
- **Logical link control (LLC) layer:** Identifies and encapsulates network layer protocols, and controls error checking and frame synchronization.

### 1 | Physical Layer
Includes the physical equipment involved in the data transfer, such as the cables and switches.  
The physical layer of both devices must also agree on a signal convention so that the 1s can be distinguished from the 0s on both devices.

## References
[Cloudflare docs](https://www.cloudflare.com/en-in/learning/ddos/glossary/open-systems-interconnection-model-osi/)
[Wikipedia](https://en.wikipedia.org/wiki/OSI_model)
[FreeCodeCamp](https://www.freecodecamp.org/news/osi-model-computer-networking-for-beginners/)