# Stream Processing
With the advent of IoT Internet Of Things, entities are generating and transmitting data online at an unprecedented rate.  
To manage the massive amount of streaming data we need to have sophisticated backend systems in place to gather meaningful information from it and archive/purge not so meaningful data.  

The more data we have, the better our systems evolve. Businesses today rely on data. Hence, stream processing key to businesses and modern software applications.  

## Data Ingestion 
The process of collecting data streaming-in from several different sources and making it ready to be processed by the system.  
Eg. Apache Storm, Apache Nifi, Apache Spark, Samza, Flink

### Layers
There are several layers to the data processing setup:
- Data collection and preparation layer:  
   In order to make the data uniform and fit for processing, it is collected and converted into a standardized format.
- Data processing layer:  
   Further processed based on business logic. Generally classified into different flows, routed to different destinations.
- Data analytics layer:  
   Analytics such as predictive modelling, statistical analytics, text analytics happen in this layer.
- Data visualization layer:  
   Once the analytics are run, we have valuable intel. This is presented before the stakeholders, generally in a dashboard such as Kibana. 
- Data storage layer: Persists data
- Data security layer: Has security systems in place for UAC, etc.

 ### Ways to ingest data
 - Real-time: For medical data like heartbeat, BP or financial applications
 - Batched: For analytics or generalized data.  
   ETL (Extract-Transform-Load) is generally used.  
   - Extract: Fetching data from multiple data sources 
   - Transform: Transforming the extracted heterogeneous data into a standardized format
   - Load: Moving the transformed data to a another location for further processing

### Challenges
- Slow process: Conversion is a challenge since data is heterogeneous; takes a lot of computing resources and time.
- Complex and Expensive: Preparation of data is expensive; formats of data might differ at any time too.
- Security: Moving data around is inherently risky, and this process has several staging areas.

### Use-cases
- Moving big data into Hadoop: Hadoop is the most popular distributed data processing framework for analysis.
- Streaming data from DBs to ElasticSearch: Sometimes whole DBs are replicated into ElasticSearch for indexing.
- Log processing: Log processing from distributed systems is a challenge, usually the ELK (Elastic LogStash Kibana) stack 
- Engines for real-time events: Live Sports

## Data Pipelines
Data pipelines facilitate the efficient flow of data from one point to another, and also enable the developers to apply filters on the data streaming-in in real-time.  

Features:
- Ensures smooth flow of data.
- Enables application of filters and business logic on streaming data.
- Averts any bottlenecks and redundancy in the data flow.
- Facilitates parallel processing of data.
- Avoids data being corrupted.

## Distributed Data-Processing
Distributed data processing means diverging large amounts of data to several different nodes, running in a cluster, for parallel processing.  
All the nodes execute the task allotted parallelly, working in conjunction with each other co-ordinated by a node-co-ordinator (such as Apache ZooKeeper).  
<img src="https://user-images.githubusercontent.com/45961072/116463142-f8041300-a887-11eb-8230-01a4faec44a2.png" height="350px" width="624px"></img>


Advantages:
- Scalable and highly available: Nodes are distributed and the tasks are executed parallelly
- Loss of data can be prevented by replicating data across nodes

