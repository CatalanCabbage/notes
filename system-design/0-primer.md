# Primer
## Beginning a solution
1. Outline use cases, constraints, and assumptions  
    - Who is going to use it, how are they going to use it?
    - How many users are there?
    - What does the system do, what are the inputs and outputs of the system?
    - How much data do we expect to handle, how many requests per second do we expect?
    - What is the expected read to write ratio?
2. Create a High level design  
Sketch the main components, justify ideas
3. Design each core component
    - System interface definition (API, etc)
    - Data model
4. Scale the design: Identify and resolve bottlenecks  
    - Load balancer
    - Horizontal scaling
    - Caching
    - Database sharding
5. Back-of-the-envelope estimation

## Temp - to add
DB Partitioning


# What are data systems, why do I care?
There are many tools that help manage data - caches, queues, DBs, etc. However, access patterns between them vary wildly.  
Many tools blur the distinction between those boundaries: Redis is a datastore used as a queue, Kafka is a message queue with DB-like durability guarantees, etc.  
This means that several of these tools are needed for operation of a single application, which results in a bigger data system built out of several smaller components. As such, the application is now responsible for the design of the system.  

At a lower level, we also need a basic understanding of how it works under the hood to choose the best fit for our application, and also to tune it to work best on our workload.

## Measuring performance
When measuring response times, the median is the "middle". If median is `100ms`, it means that half the requests take less than 100ms and the other half take more than 100ms. 
The median can also be called the "50th percentile" or "p50". In order to measure unexpected response times, a higher percentile is used - p95, p99 etc. If p99 = `100ms`, it implies that 99% of the requests are faster than `100ms`.  
Higher percentiles are called "tail latencies". These are important since they usually represent the requests of heavy-users of the application.  
