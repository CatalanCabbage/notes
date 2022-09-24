# Models
### The Hierarchial model
In the 70's, the most popular DB was IBM's Information Management System.  
It represented data like trees. When dealing with many-to-many relationships, the user had to decide whether to:  
- Duplicate data, avoiding joins but risking inconsistency
- Manually resolve references, making joins easier but more consistent

### The Network model
This was the evolution of the hierarchial model, where every record could have multiple parents. This made many-to-many relationships possible.  
To navigate data, users had to manually follow the path from a root record along links, called the **access path**.  
This worked well and made good use of limited hardware capabilities of the 70's - tape drive seeks were extremely slow and access paths could be manually optimized.  
However, it fell short when the data model needed changes - new access paths had to be derived, and updating data was difficult.

## The Relational model
The relational model lay all data out in the open - there are no complicated nested data structures.  
Data is formed by rows, we could read whatever we wanted.  
The access path is still needed in this case to make joins when many-to-many data is involved.  
However, **the query optimizer automatically derives access path** - the order of operations, indices to be used etc.  

Query optimizers for relational databases are complicated. However, the main insight here was this:  
**You need to build a query optimizer once, and all applications that use the DB can benefit from it.** It's easier to hand-code the best access path for specific queries, but the general-purpose solution wins in the long run.  


# Storage engines for Indexes
In order to find a value in a DB efficiently, we can use indexes. Well chosen indexes speed up read queries, but every index slows down writes.  

## What's stored in indexes?
Along with the key, we can either store the actual value of the key (clustered) or a pointer to the value on the heap file (non-clustered).  
In some situations, we might want to store the row value along with the key for performance. This is called a **clustered index**.  
In MYSQL's InnoDB, the primary key is always a clustered index. However, the secondary indexes refer to the primary key rather than the heap file location.  
It gets complicated if on updates the value is larger than the initial value; the key will have to be moved to a new location. All indexes need to be updated to point to the new location, or a forwarding pointer is left behind.

As a compromise between a clustered index and a non-clustered index, we can store _some_ data with the key. This allows some queries to be answered with the index alone. This is called a **covering index**.


## Hash indices 
Hash indexes can be used, where the key is the hash of the key and value is the "byte offset" or location where the value is stored. This is similar to a hashmap; Bitcask, the storage engine used in [Riak](https://riak.com/) (an enterprise noSQL DB) does this as long as all keys fit in the available RAM.  
This is well suited when the value of each key is updated frequently - such as like/dislike counts, view counts, etc.

However,  
- The hash indices must fit in memory. We can store them on the disk and retrieve them, but there's latency.  
- Range queries are inefficient on hash indices. Each key needs to be checked individually.

## Log-structured storage engines
We can use an append-only file to store data. Each new entry is a new entry that's appended to the file. Updates and deletes are also appended to the data file.  
When a delete is made, the new entry indicates this; this entry is called a "tombstone" marker.  

### How do we make sure we don't run out of space?
We can break the log into "segments" by closing log files (segment files) once it reaches a certain size.  
**Compaction**: Once that's broken down, we can process the file and discard all duplicates, keeping only the latest entry for each key.  
**Merging**: During compaction, segment files become smaller. Thus, multiple segment files can be merged into a single larger file during the compaction process.  

**Merging and compaction strategies:**  
**Size-tiered compaction**: When a group of SSTables become the same size (generally 4), they're merged into a new larger table.  
**Leveled compaction**: The key range is split into separate "levels", where compaction happens incrementally - the SSTables are just merged into a certain size and moved to the next level while maintaining sorted order among other SSTables in that level.  

Apart from this, there are other compaction strategies - Time based, ICS (Incremental), etc.  
[Scylla docs - Strategies](https://docs.scylladb.com/stable/architecture/compaction/compaction-strategies.html)  
[Scylla docs - Comparison](https://docs.scylladb.com/stable/architecture/compaction/compaction-strategies.html#id1)  

### SS Tables - Sorted String tables
Quick retrieval: If there are a low number of keys, each segment can have in-memory hash indices that store keys and offsets for quick retrieval. The snapshot of this hash index can be stored along with the segment files for crash recovery.  

**What if there are more keys than can be held in-memory?**  
We make a change to the segment files we use: we require that all keys in a segment file be sorted. These segment files are called **SS Tables**.  
If this is done, retrieval is much quicker when number of keys is large - instead of storing all keys in the hashmap, we store only the the first key of each segment in our hash index. Once we find the file that contains the key, we can search that file. If all keys are of the same size, we can even use binary search.  

This sorting can be done during the merging and compaction process, like mergesort.  
The storage engine can also hold the keys in sorted order as the writes come in: we can hold a balanced tree sorted data structure (memtable).  

### Handling crashes
When the storage engine crashes, the in-memory memtable is lost; the most recent writes are lost.  
To handle this, we can maintain another append-only log that's deleted whenever memtable is written to disk.  

### Summary
- Writes come in and they're stored in a memtable (sorted data structure)
- When memtable becomes bigger than some threshold, write it to disk as an SSTable. Writes continue in another memtable instance.  
- To serve reads, look for keys from the newest SSTable.
- Merging and compaction runs as a background job  

### Optimizations
This is slowest when trying to read a key that doesn't exist in the DB - we check all SSTables in the DB.  
In that case, we can use a bloom filter to check for non-existent keys before checking our DB.  

### Why append-only?
- Appending and segment merging are sequential write operations, which are generally much faster than random writes (LinkedList vs ArrayList!).  
- Concurrency and crash recovery are much simpler since segment files are append-only and immutable. We don't have to worry about cases where an older file is being updated and a crash happens, leaving the file in an inconsistent state.  
- The disadvantage of this causing duplication is offset by merging and compaction.


## B-Tree based storage engines
Instead of breaking the DB down into variable-sized segments like Log-structured indexes, B-Trees break the DB down into fixed-size **blocks** or **pages** (usually 4kb) and read or write them one page at a time. Each page can be identified using an address - like a pointer, but on the disk instead of in-memory.    
Retrieval is `log(n)` since this is balanced.  
This corresponds more closely to the underlying hardware since disks are also segmented into blocks.  

One page is designated the "root" of the B-tree; all lookups for reads or updates happen from here. Each page can contain multiple child references; the child pages are responsible for their respective key-ranges.  
**Branching factor**: The number of child page references in one page of the B-tree.  

If a value needs to be added, we find the page that encompasses the range of the key we want to insert and add it there. If there isn't enough space, we split it into two pages and the parent is updated with 2 child references - for the old and new pages.  

### Handling crashes
Some operations such as addition of a new key when the page is full might span multiple pages - 2 of the split pages and the parent page.  
This is a dangerous operation in terms of crashing and can corrupt pages leading to orphan pages.  
To protect against this, an append-only log, **Write-ahead log** (WAL) is maintained where the most recent B-tree modifications are written to before performing operations on the B-tree.

### Optimizations
- Instead of maintaining the WAL and writing pages directly, some DBs like LMDB use a copy-on-write mechanism - new pages are written in another place, after which the parent page is overwritten with the new pointers.  
- Abbreviate keys to increase the branching factor. We just need to know the boundaries.
- Try to lay out leaves in sequential order in the disk, but hard when new pages are added.
- Additional pointers may be added to pages to point to sibling pages instead of having to go to the parent.

### B-Trees vs LSM trees
- B-trees have a higher overhead: Complete pages must be overwritten even if only a few bytes have changed.
- B-trees can have higher storage overheads since there might be many fragmented (partially filled) pages. LSM Trees however consolidate all data during compaction and don't leave unused space.
- The compaction process in LSM-trees can interfere with live read/write operations. If the compaction process isn't able to keep up with incoming reads/writes, it can lead to disk space running out. Reads also start to take longer since they'll have to check more segment files.
- A key exists exactly in one place in B-trees; we can use this property to build transactional guarantees. We can place locks directly on the tree if we want to. This is not possible in LSM trees.




# Databases
A component used to persist data.

Classification of data:
- **Structured data**: No need to run preparation logic before processing. Eg. Stored in an SQL DB
- **Unstructured data**: No definite structure. Heterogeneous; contains streams, text, blobs, media, etc. Needs preparation.
- **Semi-structured data**: Mix of structured and unstructured data. Usually stored in XML, JSON, etc.

> Throughput: Actions (like Transactions) per second

## Why not keep everything in-memory?
- RAM costs more than disks
- Disks are much more durable

As long as the data fits in-memory, we can use the disk to store the WAL to be loaded after crashes. This provides durability.  
In-memory databases can be extended to work with data larger than the memory by writing to disk but caching the most recently used disk block, evicting the least recently used block. This is similar to what OSs do withh virtual memory, but a DB can manage memory at a more granular level since it deals with individual pages and keys.
### Why are disks slower than in-memory?
Counterintuitively, the performance advantage of in-memory databases is not that they don't need to read from disk. Even in a disk-based DB, we might never need to read from disk - the OS caches recently loaded disk blocks in memory anyway.  
They're faster since they can avoid the overheads of encoding in-memory data to a format that can be written to the disk.

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
No...SQL. Document-based (like JSON) databases.  
Built for high frequency reads and writes.  
Eventually consistent.  
Eg. MongoDB, Redis, Cassandra, Neo4J, Google Cloud datastore  

In noSQL databases, joins aren't needed for one-to-many relationships since data can be stored as nested trees.  
However, since support for joins is often weak, it gets complex for many-to-many relationships.  
When the DB doesn't support joins natively, the responsibility is shifted to application code.  

**Advantages:**
- Fast reads/writes - locality effect when you need to read large parts of the document at the same time.
- Sometimes closer to the data structures used by the application  
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

NoSQL is not schemaless; it has a schema, or we wouldn't know how to process data.  
**SQL can be considered schema-on-write**, where schema is validated on writing data. If we need to change schema, we need to perform a migration before writing data.  
This is similar to statically typed languages.    
**NoSQL is schema-on-read**, where schema is validated on reading data. Any data that doesn't conform to the expected schema is handled separately (mostly ignored).  
This is similar to dynamically typed languages.  


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
