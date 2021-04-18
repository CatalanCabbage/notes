# Databases - 2

## Types of Databases
- Relational database
- Document-Oriented database
- Graph database
- Key-value database
- Wide-column database
- Relational database
- Time-Series database
- Databases dedicated to mobile apps

### Document-Oriented database  
They store data in a document-oriented model in independent documents.    
The data is generally semi-structured and stored in a JSON-like format.  
Flexible schema - can be changed over time.  
Stores all information for a given object in a single instance in the database, and every stored object can be different from every other.  
Closer to the structure of data used in development.  

Eg. MongoDB, CouchDB, OrientDB, Google Cloud Datastore, Amazon Document DB

Effective for:  
- Flexible schema applications
- Horizontal scalability
- Performant read-writes
- Not much relational data

Use-cases:  
- Real-time feeds
- Live sports apps
- Writing product catalogues
- Inventory management
- Storing user comments
- Web-based multiplayer games

Used by [SEGA](https://www.mongodb.com/blog/post/sega-hardlight-migrates-to-mongodb-atlas-simplify-ops-improve-experience-mobile-gamers), [Coinbase](https://blog.coinbase.com/scaling-connections-with-ruby-and-mongodb-99204dbf8857)


### Graph Database
They store data in nodes/vertices and edges in the form of relationships.  
Each Node in a graph database represents an entity (a person, place, business etc).  
The Edge represents the relationship between the entities.  

Eg. Neo4J

Why pick Graph DBs over Relational DBs?  
- Easy Visualization
- Low Latency: In relational DBs, relationships (joins) are computed at run-time.  
  Here, relationships are are persisted in the DB and stored as edges, and only retrieval happens.
  
Used by [NASA](https://neo4j.com/blog/david-meza-chief-knowledge-architect-nasa/) and [Walmart](https://neo4j.com/blog/walmart-neo4j-competitive-advantage/) to gain insights from relationships.

### Key-value database
They use a simple key-value mapping method to store and quickly fetch data with minimum latency.  
The Key serves as a unique identifier and has a value associated with it.  
The value can be as simple as text or as complex as an object graph.  
The data in Key-value databases can be fetched in constant time O(1), there is no query language required to fetch the data.

Typically used to implement caching in applications due to the minimum latency they ensure.

Eg. Redis (**Re**mote **Di**ctionary **S**erver), Hazelcast, Riak, Voldemort, Memcache

Use-cases:
- Caching
- Persisting user state
- Persisting user sessions
- Managing real-time data
- Implementing queues
- Creating leaderboards in online games & web apps
- Implementing a pub-sub system
