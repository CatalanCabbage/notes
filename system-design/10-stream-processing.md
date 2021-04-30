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
   **ETL** (Extract-Transform-Load) is generally used.  
   - Extract: Fetching data from multiple data sources 
   - Transform: Transforming the extracted heterogeneous data into a standardized format
   - Load: Moving the transformed data to a another location for further processing  

Real-time processing gives quick insights, and batched processing gives results that are more accurate and comprehensive.  

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

**Features**:
- Ensures smooth flow of data.
- Enables application of filters and business logic on streaming data.
- Averts any bottlenecks and redundancy in the data flow.
- Facilitates parallel processing of data.
- Avoids data being corrupted.

---

## Distributed Data-Processing
Distributed data processing means diverging large amounts of data to several different nodes, running in a cluster, for parallel processing.  
All the nodes execute the task allotted parallelly, working in conjunction with each other co-ordinated by a node-co-ordinator (such as Apache ZooKeeper).  
<img src="https://user-images.githubusercontent.com/45961072/116463142-f8041300-a887-11eb-8230-01a4faec44a2.png" height="350px" width="624px"></img>


**Advantages**:
- Scalable and highly available: Nodes are distributed and the tasks are executed parallelly
- Loss of data can be prevented by replicating data across nodes

### Popular Technologies
#### Apache Hadoop (MapReduce implementation)
Preferred for batch processing.  
The most poular implementation of the MapReduce model.  
MapReduce is programming model written for managing distributed data processing across several different machines in a cluster - distributing tasks, running work in parallel, managing all the communication and data transfer.  

**Map**: Sorting the data based on a parameter  
**Reduce**: Summarizing the sorted data

The base Apache Hadoop framework is composed of the following modules
- **Hadoop Common**: Contains libraries and utilities needed by other Hadoop modules
- **Hadoop Distributed File System (HDFS)**: A distributed file-system that stores data on commodity machines, providing very high aggregate bandwidth across the cluster
- **Hadoop YARN**: A platform responsible for managing computing resources in clusters and using them for scheduling users' applications
- **Hadoop MapReduce**: An implementation of the MapReduce programming model for large-scale data processing
- **Hadoop Ozone**: An object store for Hadoop

#### Apache Spark
Preferred for real-time streaming.  
A unified analytics engine for large-scale data processing - basically an open-source cluster computing framework that provides high performance for both batch and real-time in-stream processing.  
It can work with diverse data sources and facilitates parallel execution of work in a cluster.  

#### Apache Storm
Preferred for real-time streaming.  
A computation system that reliably processes unbounded streams of data, doing for real-time processing what Hadoop did for batch processing.  
Was open sourced after being acquired by Twitter.  

#### Apache Kafka
Preferred for real-time streaming.  
An open-source distributed stream processing & messaging platform developed by LinkedIn.  
Used in the industry to develop real-time features such as notification platforms, managing streams of massive amounts of data, monitoring website activity & metrics, messaging, log aggregation.  

### Popular Architectures
- Lambda architecture
- Kappa architecture

#### Lambda Architecture
A distributed data processing architecture that **leverages both batch and the real-time streaming data processing approaches** to tackle the latency issues arising out of the batch processing approach.  
It joins results from both the approaches before presenting it to the end user.  

It generally has 3 layers:  
- Batch Layer: Deals with results of batch processing
- Speed Layer: Deals with results of real-time processing
- Serving layer: Combines results of batch and speed processing layers

<img src="https://user-images.githubusercontent.com/45961072/116713408-649b2100-a9f2-11eb-9027-07bdf646c851.png" height="350px" width="615px"></img>

#### Kappa Architecture
A distributed data processing architecture where all data (batched and real-time) flows through a single data streaming pipeline.  
Reduces the complexity of not having to manage separate layers for processing data.  
Use-case: Where the batch and the streaming analytics results are fairly identical.  

Has only 2 layers:  
- Speed layer: Streaming processing layer
- Serving layer: Delivers information and results

<img src="https://user-images.githubusercontent.com/45961072/116717937-13d9f700-a9f7-11eb-9209-d3603b9ee39b.png" height="300px" width="615px"></img>
