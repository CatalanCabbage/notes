# Web architecture - Others
## Event-driven architecture
There are generally 2 types of processes in apps: 
- CPU intensive: Computation-heavy like graphics rendering, running ML algorithms or handling data in enterprise systems.
- IO intensive: Messaging, editing or websites.  
**Event-driven architecture is suited for IO intensive applications.**  

In the context of web apps, IO operations are termed as events (clicks, requests, variable value changes, etc).  
A large number of events happening is called a stream of events.  

- Event-driven architecture simply means reacting to the events occurring regularly.  
- Reacts to events as opposed to sequentially moving through lines of code.  
- In order to react to the events, the system has to continually monitor asynchronous data streams.  

<img src="https://user-images.githubusercontent.com/45961072/116735980-e730da00-aa0c-11eb-81d2-9c2a86223ad7.png" height="300px" width="624px"></img>  


> Blocking: Flow of execution of a process is blocked until another process completes its execution.  
> Non-blocking: Asynchronous execution.

Event-driven architecture is also known as non-blocking or reactive architecture.  

**Advantages**:  
- Capable of handling a big number of concurrent connections
- Can scale better
- Provide a more reliable behaviour in a distributed environment
- Enable us to not worry about multi-threads, thread lock, out of memory issues due to high IO etc.

Technologies that implement this: [NodeJS](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/), [Play](https://www.playframework.com/) and [Akka.io](https://akka.io/) in Java  

## Shared nothing architecture
In a distributed system, every module has its own memory, disk, etc and shares nothing with other modules.  
Even if several modules in the system go down, the other modules stay online and unaffected, eliminating single points of failure.  

## Hexagonal Architecture
Three components:
- Ports: APIs - receives data
- Adapters: Converts the data obtained from the Ports, to be processed by Domain
- Domain: Holds business logic

All input goes through ports and adapters; the business logic lies at the centre isolated and all the input and output is at the edges of the structure.  
The goal is to make the app independent, loosely coupled and easy to test - in such a way that it can be tested by humans, automated tests, with mock databases, mock middleware, with or without a UI, without making any changes or adjustments to the code.  

## Peer-to-peer architecture (P2P)
A P2P network is a network in which computers (nodes) can communicate with each other without the need of a central server.  
The absence of a central server rules out the possibility of a single point of failure; all nodes in the network have equal rights.  

Seeder: A node which hosts the data on its system and provides bandwidth to upload the data to the network.   
Leecher: A node which downloads the data from the network.  

<img src="https://user-images.githubusercontent.com/45961072/116793230-2af61300-aae3-11eb-846d-a84aa42dd082.png" height="300px" width="524px"></img>   

Data is exchanged over TCP IP; however, it has an overlay network over TCP IP which enables used for indexing and peer discovery, allowing users to connect directly.  

Types:
- Unstructured: 
  - Formed by nodes that randomly form connections to each other.
  - Do not impose a particular structure on the overlay network by design. 
  - Unstructured networks are easy to build and allow for localized optimizations to different regions of the overlay (since no global structure is imposed).
  - Highly robust in the face of high rates of churn.
  - CPU intensive: When a peer wants to find a desired piece of data in the network, the search query must be flooded through the network to find as many peers as possible that share the data.
  - Since there is no correlation between a peer and the content managed by it, there is no guarantee that flooding will find a peer that has the desired data.
  - Protocols: Gossip, Kazaa, Gnutella
- Structured:
  - The overlay is organized into a specific topology, and the protocol ensures that any node can efficiently search the network for a file/resource, even if the resource is extremely rare.  
  - Usually implement a [distributed hash table (DHT)](https://en.wikipedia.org/wiki/Distributed_hash_table), in which a variant of [consistent hashing](https://en.wikipedia.org/wiki/Consistent_hashing) is used to assign ownership of each file to a particular peer.
  - Less robust in networks with a high rate of churn since remapping must occur.
  - Eg: BitTorrent, Tixati (torrent client), Storm botnet, YaCy (distributed search engine)
- Hybrid:
  - Combination of peer-to-peer and client–server models.
  - A common hybrid model is to have a central server that helps peers find each other.
  - Have better performance than either pure unstructured/structured networks since certain functions such as searching require a centralized functionality but benefit from the decentralized aggregation of nodes provided by unstructured networks.


Advantages:  
- Resilience and Availability: No single point of failure, unlike client-server
- Own your data: No central server has autonomy over data  

Eg. Blockchain, torrents, Crypto
