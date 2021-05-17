# Web architecture - Peer-to-peer (P2P)
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
