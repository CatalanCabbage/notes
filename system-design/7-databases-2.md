# Databases

## Types of Databases
- Relational database
- Document-Oriented database
- Graph database
- Key-value database
- Time-Series database
- Wide-column database
- Columnar database
- Databases dedicated to mobile apps

### Document-Oriented database  
They store data in a document-oriented model in independent documents. Best for self-contained documents.    
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
Useful when there are relationships across all data.

Eg. Neo4J

Why pick Graph DBs over Relational DBs?  
- Easy Visualization
- Low Latency: In relational DBs, relationships (joins) are computed at run-time.  
  Here, relationships are are persisted in the DB and stored as edges, and only retrieval happens.
  
Used by [NASA](https://neo4j.com/blog/david-meza-chief-knowledge-architect-nasa/) and [Walmart](https://neo4j.com/blog/walmart-neo4j-competitive-advantage/) to gain insights from relationships.

#### Can we use SQL databases for graphs?
Yes, but it gets complex. In a relational DB, we know in advance exactly what tables to join to get the data we want.  
In graph queries, we need to traverse a variable number of edges before we get the data we want - ie., joins aren't fixed. We can simulate this using recursive common table expressions though.  

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

### Time-series database

> Time Series Data: Data containing data points associated with the occurrence of an event with respect to time.

Optimized for tracking & persisting time series data.  
Time-Series data is tracked, monitored and then finally aggregated based on certain business logic.  
Useful for applications that deal with data that streams in real time, like running analytics.

Eg. Influx DB, Timescale DB, Prometheus


Use-cases: 
- IoT devices 
- Self-driving vehicles
- Industry sensors
- Social networks
- Stock market financial data
- Analytics

Used by [Spiio](https://www.influxdata.com/customer/customer-case-study-spiio/) - an IoT sensor solutions org and [IBM](https://www.influxdata.com/customer/oracle/) for monitoring.

### Wide-column database
The Wide-column database uses typical tables, columns, and rows.  
But unlike relational databases, columnar formatting and names can vary from row to row inside the same table.  
Each column can hold multiple key-value pairs, enabling it to add any new keys on the fly.  
Built for quick writes. Ref. [Cassandra perf](https://www.scnsoft.com/blog/cassandra-performance)  

Eg.  Cassandra, HBase, Google BigTable, Scylla DB

Use-case:  
Say the medical history of patients is to be stored; it varies widely for each person.  
Eg. 
Relational DB:  
|Patient|has had fever?|had polio shots at|took x meds at|has headache|lactose intolerant|regularly takes vitamins|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|A|yes|3|12th Jan, 2021|null|null|null| 
|B|null|null|null|no|yes|yes| 

Wide-column DB:
|Patient|History|
|:---:|:---:|
|A| "has had fever? : yes", "had polio shots at : 3", "took x meds at: 12th Jan, 2021"|  
|B| "has headache? : no", "lactose intolerant? : yes", "regularly takes vitamins : yes"|

If we used a relational DB here, Patient A and B would both need to have 6 columns and unused columns will be `null`.  
But if wide-column database is used, they can store their corresponding key-values in their rows, and they don't need to overlap.  

[Good reference - SO](https://stackoverflow.com/a/62027444/12415069)

### Columnar database
A columnar (column-oriented) database stores data tables by column rather than by row.  
- Low IO requirements: When all values in a column are needed: reduces the overall disk I/O requirements and reduces the amount of data you need to load from disk, since they're stored close together.  
- Easy compression: Since column values are stored together, their data-type is the same and they can be compressed easily.  
- Vectorized processing is possible: running queries directly on compressed data. 
 
Use-case: Typically for analytical operations in data warehouses.  
Used a a part of ETL: Data is **extracted** for OLAP purposes from OLTP DBs, **transformed** into an analysis-friendly schema and then **loaded** in the data warehouse.

Eg. Say a row has 100 columns. You run a query involving 5 columns.  
In RDBs, all rows are fetched and then columns are filtered - 100% data is read.  
In wide-column DB, only those 5 columns are read; and since they're close to each other wrt storage blocks, no filtering is needed; only 5% of data is read.   

**Data cubes** are also used to assist in analytical queries. Data cubes are matrices that store aggregate dimensions of data like sum, etc. Values are directly taken from this instead of computing them again. Overhead: They need to be updated when new data is retrieved.   

Eg. PostgreSQL c-store, InfiniDB, Amazon Redshift, Microsoft Azure SQL Data Warehouse, Google BigQuery

[Good reference: Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/dg/c_columnar_storage_disk_mem_mgmnt.html)
