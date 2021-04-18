# Databases
A component used to persist data.

Classification of data:
- **Structured data**: No need to run preparation logic before processing. Eg. Stored in an SQL DB
- **Unstructured data**: No definite structure. Heterogeneous; contains streams, text, blobs, media, etc. Needs preparation.
- **Semi-structured data**: Mix of structured and unstructured data. Usually stored in XML, JSON, etc.

## Relational (SQL) Databases
Modelled based on the normalized relationship between data. ACID compliant.  
Eg. PostgreSQL, MySQL, Amazon Aurora, MariaDB, Google Cloud SQL, MSSQL

**Relationships**:
- One to one: ID -> Name
- One to many: City -> Many ZIP Codes
- Many to Many: Book -> Many categories, Category -> Many books

**Normalization**:  
A unique entity occurs only in one place/table, in its simplest and atomic form and is not spread throughout the database.  
This helps uphold Data Consistency.


**Advantages:**  
- Optimal when relational data needs to be stored.
- Transactions and ACID compliant 

**Disadvantages:**
- Needs to be sharded or replicated to scale


## NoSQL Databases
No...SQL. More like JSON-based databases.  
Built for high frequency reads and writes.  
Eventually consistent.  
Eg. MongoDB, Redis, Cassandra, Neo4J, Google Cloud datastore

**Advantages:**
- Fast reads/writes
- Can scale easily - can add new server nodes on the fly
- Designed to run intelligently on clusters
- No schema, flexible

**Disadvantages:**
- Inconsistent (because it's not normalized). Only eventually consistent
- No ACID Transactions compliance
- No schema, can become dev-unfriendly

> War story: YouTube - Often we see a video with 10 views and 15 likes. How is this even possible?   
> Because it's eventually consistent. The views is inconsistent and takes a while to be updated.  
 

## SQL vs NoSQL
No definite answers, depends on use-cases.  
Performance: Depends on design; SQL with a lot of joins will inevitably take more time.  
SQL with no relationships and joins == NoSQL.  

[Why Quora uses SQL and not NoSQL](https://www.quora.com/Why-does-Quora-use-MySQL-as-the-data-store-instead-of-NoSQLs-such-as-Cassandra-MongoDB-or-CouchDB-Are-they-doing-any-JOINs-over-MySQL-Are-there-plans-to-switch-to-another-DB)

---
## ACID Transactions:  
- If a transaction occurs, it will be executed as a single operation without affecting other transactions.
- There are only 2 states: Before the transaction and after the transaction. Both states are consistent and durable. No middle state.  

A - Atomicity: A transaction either succeeds, or nothing happens. All or nothing.  
C - Consistency: No integrity constraints are broken.  
I - Isolation: Multiple transactions can happen concurrently without one affecting the other.  
D - Durability: The effects (state) of a completed transaction are never lost.  

## Polyglot Persistence
Using several different persistence technologies to fulfil different persistence requirements in an application.  
However, integration of all data stores increases complexity.  

Eg: An FB-esque application:
|Feature|DB|Example|
|:---:|:---:|:---:|
|Friends, details|Relational DB|PostgreSQL|
|Caching, sessions|Key-Value store|Redis, Memcache|
|Run analytics|Wide Column DB|Cassandra, HBase|
|Recommendations|Graph DB|Neo4j|
|Search|Document-oriented data store|Elasticsearch|

<img src="https://user-images.githubusercontent.com/45961072/115062149-a5316f80-9f07-11eb-8ae8-1a577691b1c2.png" height="399px" width="624px"></img>

### Multi-model DBs
DB capable of using different data models in a single database system.  
Reduce complexity, since all types are persistence are exposed via a single API.  
Eg. ArangoDB, CosmosDB, OrientDB, CouchBase

## Consistency
### Eventual consistency
All server nodes are updated at different speeds, so they might not be strictly consistent at a particular instance.
A consistency model which **enables the data store to be highly available and scalable**.  
Also known as optimistic replication, key to distributed systems.

Eg. Say there's a blog; it has replicated datastores throughout the world.  
Since there are so many nodes running, there is no single point of failure.  
The data store service is highly available. Even if a few nodes go down the persistence service as a whole is still up.  
When an action is performed in one node, it takes time to be replicated across all instances (become consistent).  

Use-cases:  
- Non mission-critical applications
- Social media, video stats, website analytics

**Advantages**:  
- New nodes can be added on the fly
- Suitable for applications that don't need exact values immediately (like likes on a post)
- Allows the system flexibility, so it can be highly available

### Strong consistency
All the server nodes across the world should contain the same value of an entity at any point in time.  
Generally done by locking the elements being changed, preventing concurrent modification and lifting locks only after changes are propagated.  
Impacts HA and scalability, since time spent waiting for locks to be lifted is time wasted.  
ACID compliant transactions.  

Use-cases: Banking, Stock-markets  

## CAP Theorem
CAP (Consistency, Availability, Partition Tolerance) - you can't have all 3, [you need to choose](https://youtu.be/9giCebGY8-s?t=293) any two.  

> Partition: A communications break within a distributed system — a lost or temporarily delayed connection between two nodes.  
> Partition tolerance: Clusters must continue to work despite any number of communication breakdowns between nodes in the system.
  
- **CP database**:  
  - Delivers consistency and partition tolerance at the expense of availability.  
  - **When a partition occurs between any two nodes, the system has to shut down the inconsistent node** (i.e., make it unavailable) until the partition is resolved.  
  - Thus at the time of partition, some nodes are unavailable.  
- **AP database**:  
  - Delivers availability and partition tolerance at the expense of consistency.  
  - **When a partition occurs, those at the wrong end of a partition might return an older version of data than others**, but they all remain available.  
  - When the partition is resolved, the AP databases typically resync the nodes to repair all inconsistencies in the system.  
  - Thus at the time of partition, some nodes are inconsistent.
- **CA database**:  
  - Delivers consistency and availability across all nodes; does not support partitioning.  
  - It can’t do this if there is a partition between any two nodes in the system, and therefore can’t deliver fault tolerance.
  - **Generally unsuited for distributed applications, since partitions cannot be avoided in distributed systems.**
  - **Can be achieved by using a relational DB with replicated nodes**.

### MongoDB and CAP - CP
MongoDB is a CP data store — it resolves network partitions by maintaining consistency, while compromising on availability.  
**It is a single-master system**: each replica set can have only one primary node that receives all the write operations.  
All other nodes in the same replica set are secondary nodes that replicate the primary node's operation log and apply it to their own data set.  

**When the primary node becomes unavailable, the secondary node with the most recent operation log will be elected as the new primary node.**  
Once all the other secondary nodes catch up with the new master, the cluster becomes available again.  
**Clients can't make any write requests during this interval and the data remains consistent across the entire network.**

### Cassandra and CAP - AP
Cassandra **has a masterless architecture**, and so has multiple points of failure, rather than a single one.  
Because Cassandra doesn't have a master node, all the nodes must be available continuously to accept writes.  
However, it provides eventual consistency by allowing clients to write to any nodes at any time and then syncing it across all nodes as quickly as possible.  

Hence, **all nodes stay available always; but since it takes time to sync writes across all nodes, it's not consistent** (promises only eventual consistency).
